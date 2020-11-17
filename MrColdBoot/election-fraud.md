Election Fraud
==============

| Category | Points |
| -------- | ------ |
| Misc     | 900    |

In this challenge, we are given the following instructions along with a file named **dundie_votes.zip**.

> The integrity of the Dundie Awards is in question. We need you to
> review the ballots, tell us who the winner is and how many legitimate
> votes there were for Pam, Dwight, Michael and Todd.

## Getting Started

Lets begin by extracting the contents of the zip file and seeing what's there.

```bash
$ unzip -q dundie_votes.zip
$ ls -l
-rw-r--r-- 1 coldboot coldboot   26074 Nov 13 17:49 ballot_0.png
-rw-r--r-- 1 coldboot coldboot   26073 Nov 13 17:55 ballot_100.png
-rw-r--r-- 1 coldboot coldboot   26076 Nov 13 17:49 ballot_10.png
...
...
-rw-r--r-- 1 coldboot coldboot 26072 Nov 13 17:55 ballot_99.png
-rw-r--r-- 1 coldboot coldboot 26076 Nov 13 17:49 ballot_9.png
-rw-r--r-- 1 coldboot coldboot  8071 Nov 13 18:13 ballots.txt

```

We have about a hundred PNG images, which are presumably the ballots, and a single *ballots.txt* file. If we inspect the contents of this text file we will see that each line is composed of a filename matching one of the ballots, and a long string consisting of letters and numbers.

```bash
$ cat ballots.txt
ballot_0.png,F1BAE20C7C17BA2767FA2327C18A8F0B938FE763D78D026ED2C2180B798DB932
ballot_1.png,D6B737FEFCC1C0BB0980F18E8C8E4A07CC0C8B1559442B69E4967E8DF19AF139
...
...
ballot_99.png,CC09D63A8611B54F7B4A2B924EDDBD6688F0655CB40EB14F3A6BD53F28E28B45
```

Upon closer inspection we can see the long string appears to be hex, using only letters A-F.

> CC09D63A8611B54F7B4A2B924EDDBD6688F0655CB40EB14F3A6BD53F28E28B45

Ok, this looks promising. Experience suggests its a checksum, specifically SHA-256. These checksums are often used to validate the *integrity* of a file or piece of data. The hint we were given mentioned counting *legitimate* ballots, and this is likely how we'll determine which ballots those are.

Before diving in though, lets look at some of the ballot files to make sure there are no surprises.

If we open one of the *ballot_xx.png* files we will see the following image. Its important to note that the background is actually transparent. We have the black border and lettering, and gray filling in the circle of the chosen candidate.

![ballot][ballot_sample]

Easy enough I suppose, it sounds like we should be able to determine legitimate ballots, then just open them all one by one and count them... they're probably mostly fake anyways. I blame Dwight.

## Determining the legitimate votes

### A brief explanation of cryptographic hash functions (aka. message digests)

SHA-256 is a cryptographic hash algorithm that is widely used to provide a unique *fingerprint* for a sequence of bytes of arbitrary length. These algorithms provide certain characteristics that make them useful for verifying data *integrity*. Some of these include:

-   The same input will always generate the same output.
-   The output will always be the same length regardless of the length of
    the input. (SHA-256 is always 256 bits, SHA-512 is 512 bits)
-   The output will always be unique (in practice).
-   The function is irreversible, meaning the input cannot be derived from the
    output.
-   Changing even a single bit of the input will result in dramatically
    different output, making it difficult to determine whether two inputs
    are similar.

This makes them useful in many different applications including digital signatures, message authentication, TLS/SSL certificates, and storing passwords.

SHA-256 and SHA-512 are generally considered the standard today. Older algorithms that you may still see include SHA-1 and MD5. Both of these are considered insecure and shouldn't be used in a security context today.

### Back to the challenge

Before we continue, we can use the following command to cleanup the line endings since the text file was generated on Windows, and *nix systems are a little different.

```bash
sed -i 's|\r||g' dundie_votes/ballots.txt
```

Now, let's start by using `cat` to look at the *ballots.txt* file. We see the list of files, and presumably, their checksums.

```bash
$ cat dundie_votes/ballots.txt
ballot_0.png,F1BAE20C7C17BA2767FA2327C18A8F0B938FE763D78D026ED2C2180B798DB932
ballot_1.png,D6B737FEFCC1C0BB0980F18E8C8E4A07CC0C8B1559442B69E4967E8DF19AF139
...
```

Next, let's convert the file into the format used by the `sha256sum` command by piping it through `awk` to swap the fields and convert the checksum to lower-case letters.

```bash
$ cat dundie_votes/ballots.txt | \
    awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}'
f1bae20c7c17ba2767fa2327c18a8f0b938fe763d78d026ed2c2180b798db932  ballot_0.png
d6b737fefcc1c0bb0980f18e8c8e4a07cc0c8b1559442b69e4967e8df19af139  ballot_1.png
cd2b0d0c64f24db8e64227910347525e50cfdfd3f589ded8f1456a51fc76724f  ballot_10.png
...
```

Finally, we pipe that output through `sha256sum`. We see that most of the file hashes match, but not all. This confirms we are indeed working with SHA-256 and that we need to reject the bad ballots.

```bash
$ cat dundie_votes/ballots.txt | \
    awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}' | \
    (cd dundie_votes/ && sha256sum -c)
ballot_22.png: OK
ballot_23.png: OK
ballot_24.png: FAILED
ballot_25.png: FAILED
ballot_26.png: OK
...
...
sha256sum: WARNING: 19 computed checksums did NOT match
```

Lets go ahead and further clean up the output by using `grep` to only show lines that contain *OK*, and `sed` to strip everything but the filename.

```bash
$ cat dundie_votes/ballots.txt | \
    awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}' | \
    (cd dundie_votes/ && sha256sum -c) | \
    grep OK | sed 's|:.*||'
ballot_0.png
ballot_1.png
ballot_10.png
...
```

## Counting the ballots

Now that we know which ballots to count, we just need to open each one and read the vote. We hate repetitive tasks that can be easily automated, so we need to write something that will essentially perform the following tasks:

-   Read the image data
-   Locate the gray pixels
-   Associate those pixels with a candidate, based on their location in the file

We could read up on the format of PNG files and determine how to extract color information, then map it to coordinates or address ranges, but it likely uses compression and won't be trivial. Anyways, thats not how we want to spend our precious CTF time.

So, we are going to use OpenCV with python. OpenCV will take care of loading the PNG image into a convenient container for us to work with and is fairly easy to get started with, especially in python. If you have python installed, you can install the opencv bindings with `pip install opencv-python`.

If we start with one of the OpenCV example programs and tweak it a bit, we end up with the following new script which we'll name *ballots.py*

```python
#!/usr/bin/env python
# ballots.py
import sys
import cv2 as cv
import numpy as np

# These coordinates represent the rectangles that cover
# the areas to be inspected on the ballot. They are specified
# in the format '[top, bottom, left, right]'
coordinates = {
    'pam': [178, 216, 55, 110],
    'michael': [236, 276, 55, 110],
    'dwight': [290, 330, 55, 110],
    'todd': [348, 386, 55, 110],
}

# Read filename from stdin
filename = sys.argv[1]

# Loads an image from filename. This returns a special container
# type that handles all the colors and alpha channels for us.
# It can easily be accessed as a simple multi-dimensional array.
src = cv.imread(cv.samples.findFile(filename), cv.IMREAD_COLOR)

# Check for error
if src is None:
    print ('Error: failed to load image')
    exit(1)

# Convert to grayscale. Text and alpha is black. Only vote > 0
gray = cv.cvtColor(src, cv.COLOR_BGR2GRAY)

# Inspect each candidates box looking for votes.
# If countNonZero() returns a large number, count it!
votes = []
for k,v in coordinates.items():
    box = gray[v[0]:v[1], v[2]:v[3]]  # This is the area to search
    if(cv.countNonZero(box) >= 500):  # This counts the pixels in the box
        votes += [k]

# Make sure they only voted for one candidate
if len(votes) > 1:
    print('Error: can only vote for one candidate!', fp=sys.stderr)
    exit(1)

# Finally, print the candidate selected on this ballot
print(votes[0])
```

There are two key parts to this:

1.  We use `cv.cvtColor()` to convert the image to gray-scale. This will combine the RGB color channels into a single 8-bit channel, so each pixel will be represented by a value ranging from 0 to 255. Both the transparent background and the black borders and text will be '0'. This means the only pixels with a value > 0 are the gray circles used to fill in the vote.

2.  We then use `cv.countNonZero()` to count how many non-zero pixels are in each of the candidates voting space on the ballot. Since the only non-zero pixels in the image are from the filled circle, a large return value indicates a selected candidate.

We can test the script with the following command.

```bash
$ python ballots.py dundie_votes/ballot_65.png
dwight
```

Its for dwight!

## Putting it all together

At this point we know what ballots to count, and can read a ballot programmatically. Let's continue with our bash pipe madness and get
this flag.


We'll pipe the list of filenames from before into a loop which will run the python script *ballots.py* on each file. This outputs a steady stream of names.

These are legitimate votes! We're getting close now.

```bash
$ cat dundie_votes/ballots.txt | \
    awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}' | \
    (cd dundie_votes/ && sha256sum -c) | \
    grep OK | sed 's|:.*||' | \
    while read filename; do \
        python ballots.py dundie_votes/$filename; \
    done
...
...
pam
michael
michael
pam
michael
...
```

This can be piped through the `sort` and `uniq -c` command to give us a list of names and how many votes they have. The last `sort -nr` command will sort the final results numerically in descending order.

```bash
$ cat dundie_votes/ballots.txt | \
    awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}' | \
    (cd dundie_votes/ && sha256sum -c) | \
    grep OK | sed 's|:.*||' | \
    while read filename; do \
        python ballots.py dundie_votes/$filename; \
    done | \
    sort | uniq -c | sort -nr
```

And this is our output. Here we have it, the number of legitimate votes for each candidate.

```
27 pam
20 todd
20 dwight
15 michael
```

Pam  wins!

## Submitting the flag

> tell us who the winner is and how many legitimate votes there were for Pam, Dwight, Michael and Todd.

It may take a few attempts, but if we focus on what we were told in the hint,
and take into account that most of the flags include hyphens, we can pretty confidently figure it out from here. One more hint, case matters! Look at the names exactly as they are are written in the hint.

## tl;dr

1. Extract zip file

2. Create the *ballots.py* python script above

3. Run this really long bash command

```bash
cat dundie_votes/ballots.txt | awk 'BEGIN {FS=","}; {print tolower($2) "  " $1}' | (cd dundie_votes/ && sha256sum -c) | grep OK | sed 's|:.*||' | while read filename; do python ballots.py dundie_votes/$filename; done | sort | uniq -c | sort -nr
```

[ballot_sample]: ../media/ballot_37.png "Ballot Sample"
