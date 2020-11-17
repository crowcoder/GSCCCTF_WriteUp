A Code Camp Story
=================

| Category     | Points |
| ------------ | ------ |
| Cryptography | 1000   |

> (11/14) Portsmouth, Londonderry, and Plymouth

In this challenge, we find these three clues scattered in the online venues for Portsmouth, Londonderry, and Plymouth.

> W N O R P Q S U T V Y Z X A C E B G H F D J I L G K
>
> 12 11 02 03 25 11 04 24 16 25 18 23 21 06 24 03 25 24 05 09 19 04 18 23 11
>
> B 12

## The clues

Lets take a closer look at the first hint.

```
W N O R P Q S U T V Y Z X A C E B G H F D J I L G K
```

We notice its basically each letter of the alphabet, not quite in order, but close enough that it likely doesn't contain a message or flag. Also, `G` appears twice and `M` is missing. This is probably a key of some sort.

Now let's look at the second clue, the sequence of numbers.

```
12 11 02 03 25 11 04 24 16 25 18 23 21 06 24 03 25 24 05 09 19 04 18 23 11
```

Here we notice some numbers appear more than once. there are even some patterns like `18 23` that appear twice. These are the kinds of patterns we find in language. We can also see the largest value is `25`, less than the letters of the alphabet. Its looks like this might represent letters and words, so this is probably an encoded message.

Now the third clue,

```
B 12
```

This is really short, it's not likely part of the message. It could be part of the key, or perhaps the key and/or message is encoded with these. Maybe `B` is the start of the key, and `12` is the offset for the message? It doesn't really matter.

## A Weak Cipher

If indeed the numbers are the message, and the sequence of letters the key,
it could just be a constant 1-to-1 encoding where each number maps to a specific letter, ie. `12` would map to the 12th letter in the series, `03` the third letter.

If we attempt to decode it that way as is, we get `ZYNOGYRL....`, probably not what where looking for. If we shift the letters so `B` is the first letter we don't do much better. However, there are only 26 possibilities if we keep rotating the key like this.

With a little bit of python, we can rotate through each of these possibilities automatically. For example, for a key `ABCD`, each letter is shifted left like so: `BCDA`, `CDAB`, `DABC`, and back to `ABCD`.

Heres a short python script we will call *story.py*

```python
# story.py
hint1 = 'W N O R P Q S U T V Y Z X A C E B G H F D J I L G K'
hint2 = '12 11 02 03 25 11 04 24 16 25 18 23 21 06 24 03 25 24 05 09 19 04 18 23 11'

# Parse the key into an array of characters ['W', 'N', ...]
key = hint1.split()

# Rotate the key through each possibility of print it out
for i in range(0, len(key)):
    print(''.join(key[i:] + key[:i]))
```

When we run this, we can see how the values are rotated.

```bash
$ python story.py
WNORPQSUTVYZXACEBGHFDJILGK
NORPQSUTVYZXACEBGHFDJILGKW
ORPQSUTVYZXACEBGHFDJILGKWN
RPQSUTVYZXACEBGHFDJILGKWNO
PQSUTVYZXACEBGHFDJILGKWNOR
QSUTVYZXACEBGHFDJILGKWNORP
SUTVYZXACEBGHFDJILGKWNORPQ
UTVYZXACEBGHFDJILGKWNORPQS
TVYZXACEBGHFDJILGKWNORPQSU
VYZXACEBGHFDJILGKWNORPQSUT
...
...
```

## A solution

Now lets see what happens when we attempt to decode the message with each of these. First, update the script to look like the following.

```python
# story.py
hint1 = 'W N O R P Q S U T V Y Z X A C E B G H F D J I L G K'
hint2 = '12 11 02 03 25 11 04 24 16 25 18 23 21 06 24 03 25 24 05 09 19 04 18 23 11'

# Parse the key into an array of characters ['W', 'N', ...]
key = hint1.split()
msg = [int(c) for c in hint2.split()]

# Rotate the key through each possibility of print it out
for i in range(0, len(key)):
    rotated_key = key[i:] + key[:i]
    decoded_msg = [rotated_key[c-1] for c in msg]
    print(''.join(rotated_key), ''.join(decoded_msg))
```

`msg = [int(c) for c in hint2.split()]` parses `hint2` into an array of integers, while the line `decoded_msg = [rotated_key[c-1] for c in msg]` decodes the message.

When we run this, it will print out each of the possible keys like before, along with the message as decoded with that key. If we are correct in assuming how the cipher works, one of these should contain recognizable words.

```bash
$ python story.py
WNORPQSUTVYZXACEBGHFDJILGK ZYNOGYRLEGGIDQLOGLPTHRGIY
NORPQSUTVYZXACEBGHFDJILGKW XZORKZPGBKHLJSGRKGQVFPHLZ
ORPQSUTVYZXACEBGHFDJILGKWN AXRPWXQKGWFGIUKPWKSYDQFGX
RPQSUTVYZXACEBGHFDJILGKWNO CAPQNASWHNDKLTWQNWUZJSDKA
PQSUTVYZXACEBGHFDJILGKWNOR ECQSOCUNFOJWGVNSONTXIUJWC
QSUTVYZXACEBGHFDJILGKWNORP BESURETODRINKYOUROVALTINE
SUTVYZXACEBGHFDJILGKWNORPQ GBUTPBVRJPLOWZRTPRYCGVLOB
UTVYZXACEBGHFDJILGKWNORPQS HGTVQGYPIQGRNXPVQPZEKYGRG
TVYZXACEBGHFDJILGKWNORPQSU FHVYSHZQLSKPOAQYSQXBWZKPH
VYZXACEBGHFDJILGKWNORPQSUT DFYZUFXSGUWQRCSZUSAGNXWQF
YZXACEBGHFDJILGKWNORPQSUTV JDZXTDAUKTNSPEUXTUCHOANSD
ZXACEBGHFDJILGKWNORPQSUTVY IJXAVJCTWVOUQBTAVTEFRCOUJ
XACEBGHFDJILGKWNORPQSUTVYZ LIACYIEVNYRTSGVCYVBDPERTI
ACEBGHFDJILGKWNORPQSUTVYZX GLCEZLBYOZPVUHYEZYGJQBPVL
CEBGHFDJILGKWNORPQSUTVYZXA KGEBXGGZRXQYTFZBXZHISGQYG
EBGHFDJILGKWNORPQSUTVYZXAC WKBGAKHXPASZVDXGAXFLUHSZK
BGHFDJILGKWNORPQSUTVYZXACE NWGHCWFAQCUXYJAHCADGTFUXW
GHFDJILGKWNORPQSUTVYZXACEB ONHFENDCSETAZICFECJKVDTAN
HFDJILGKWNORPQSUTVYZXACEBG ROFDBOJEUBVCXLEDBEIWYJVCO
FDJILGKWNORPQSUTVYZXACEBGH PRDJGRIBTGYEAGBJGBLNZIYER
DJILGKWNORPQSUTVYZXACEBGHF QPJIHPLGVHZBCKGIHGGOXLZBP
JILGKWNORPQSUTVYZXACEBGHFD SQILFQGHYFXGEWHLFHKRAGXGQ
ILGKWNORPQSUTVYZXACEBGHFDJ USLGDSKFZDAHBNFGDFWPCKAHS
LGKWNORPQSUTVYZXACEBGHFDJI TUGKJUWDXJCFGODKJDNQEWCFU
GKWNORPQSUTVYZXACEBGHFDJIL VTKWITNJAIEDHRJWIJOSBNEDT
KWNORPQSUTVYZXACEBGHFDJILG YVWNLVOICLBJFPINLIRUGOBJV
```
And there it is, on line 6:

```
QSUTVYZXACEBGHFDJILGKWNORP BESURETODRINKYOUROVALTINE
```

**BESURETODRINKYOUROVALTINE**

## One last note

It was possible that the message was rotated the same way the key was. We got lucky and it ended up being correct, but it didn't really matter since each letter would have still been decoded correctly, just in the wrong place. When we decoded the message, it wouldv'e looked something like **NKYOUROVALTINEBESURETODRI**. We could still recognize words like *YOUR* and *BE SURE*, and easily put it back into place.
