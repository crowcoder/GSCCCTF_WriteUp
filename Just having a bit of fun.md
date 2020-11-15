# Just Having a Bit of Fun

The challenge clue is:

> The flag is NEFCUiUUcAlCIGAjAWM or RGBgIwlmAyQxcEFAZRA

You are expected to recognize or guess that the two nonsense words are base64 encoding. So naturally you decode them.. but you don't get a readable value.

If you were to decode these in a programming language, you would probably have to decode into bytes and then into a string. So, here we're hoping you look at them as bytes and take a hint from the "or" in the clue. Also meant to be a hint is the word "Bit" in the title.

If you apply the bitwise OR operator btye by byte between the two values you end up with a new set of bytes. This set of bytes is also base64 encoded, however, this one does decode into a readable string.

|| byte 1 | byte 2 | byte 3 | byte 4 | byte 5 | byte 6 | byte 7 | byte 8 | byte 9 | byte 10 | byte 11 | byte 12 | byte 13 | byte 14 |
|:---:| :---: | :---: | :---: | :---:  | :---:  | :---: | :---:  | :---:  | :---: | :---:  | :---:  | :---: | :---:  | :---:  |  
|NEFCUiUUcAlCIGAjAWM| 00110100 | 01000001 | 01000010 | 01010010 | 00100101 | 00010100 | 01110000 | 00001001 | 01000010 | 00100000 | 01100000 | 00100011 | 00000001 | 01100011 |
|| OR | OR | OR | OR |  OR |  OR |  OR |  OR |  OR |  OR |  OR |  OR |  OR |  OR | 
 |RGBgIwlmAyQxcEFAZRA| 01000100 | 01100000 | 01100000 | 00100011 | 00001001 | 01100110 | 00000011 | 00100100 | 00110001 | 01110000 | 01000001 | 01000000 | 01100101 | 00010000 |
 ||   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   ==   |   
|| 01110100 | 01100001 | 01100010 | 01110011 | 00101101 | 01110110 | 01110011 | 00101101 | 01110011 | 01110000 | 01100001 | 01100011 | 01100101 | 01110011 |  
  
  <br />

 These bytes when converted to a UTF8 or ASCII string reveal the flag.  

This is the result when encoded as base64: `dGFicy12cy1zcGFjZXM=`.   

[Here is a LINQPad query that solves this challenge](http://share.linqpad.net/99weoo.linq)