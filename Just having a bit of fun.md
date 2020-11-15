# Just Having a Bit of Fun

The challenge clue is:

> The flag is NEFCUiUUcAlCIGAjAWM or RGBgIwlmAyQxcEFAZRA

You are expected to recognize or guess that the two nonsense words are base64 encoding. So naturally you decode them.. but you don't get a readable value.

If you were to decode these in a programming language, you would probably have to decode into bytes and then into a string. So, here we're hoping you look at them as bytes and take a hint from the "or" in the clue. Also meant to be a hint is the word "Bit" in the title.

If you apply the bitwise OR operator btye by byte between the two values you end up with a new set of bytes. This set of bytes is also base64 encoded, however, it does decode into a readable string.

[Here is a LINQPad query that solves this challenge](http://share.linqpad.net/99weoo.linq)