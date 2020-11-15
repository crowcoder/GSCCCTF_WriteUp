# Election Fraud

The clue is:

> The integrity of the Dundie Awards is in question. We need you to review the ballots, tell us who the winner is and how many legitimate votes there were for Pam, Dwight, Michael and Todd.

Additionally, there is a download link. When you unzip the download you will see approximately 100 "ballots" and one text file.

![ballot](./media/ballot_37.png)

The text file is a list of every ballot and a string of hecidecimal values. What could this be...?? 

Like many of the clues, the wording is chosen deliberately. "integrity" is meant to give you a hint that maybe the file integrity is what you need to determine. A little research might lead you to find that SHA256 hashes are a common way to communicate file integrity. If you hash a file and arrive at a different value than what it is supposed to be, then you know that the file has been tampered with because it is astronomically improbable that a file will hash the same after a change has been made, no matter how small.

Now that you figured out what was going on, you have the tedious task of hashing all the files and comparing the result to the expected result. Throwing out the files where the hashes do not match, you then need to view all the "valid" images and count the votes.

The flag format is : `the winner's first name-Pam votes-Dwight votes-Michael votes-Todd votes`

An easy way to acquire the SHA256 hash of the ballots is to use PowerShell. With a little more code that reads files from a folder and does a loop you can dump them all reasonably easily.

`Get-FileHash -Path C:\..path to file..\ballot_xx.png`
