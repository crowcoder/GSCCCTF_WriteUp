# Miracle Max

The clue:

> Have fun storming the castle!

Additionally, there is a link to a page that looks a lot like Case of the Mondays. However, the premise is not the same. In this case, the bold letters represent 1's and the others represent 0's. You throw away the spaces.

```html
<p>T<b>he</b>r<b>e</b>'s <b>a</b> b<b>ig</b> <b>d</b>i<b>f</b>fere<b>n</b>c<b>e</b> <b>b</b>e<b>t</b>w<b>een</b> m<b>ost</b>l<b>y</b> <b>d</b>e<b>ad</b> <b>an</b>d <b>all</b> d<b>e</b>a<b>d</b>. <b>Pl</b>e<b>as</b>e, o<b>pe</b>n h<b>i</b>s mou<b>t</b>h<b>.</b> <b>N</b>o<b>w</b>, <b>mos</b>t<b>l</b>y de<b>ad</b> is sl<b>i</b>g<b>ht</b>l<b>y</b> a<b>li</b>v<b>e.</b> No<b>w</b>, <b>a</b>ll <b>d</b>e<b>ad</b>.<b>w</b>e<b>ll</b>, wit<b>h</b> al<b>l</b> d<b>ea</b>d<b>,</b> t<b>he</b>r<b>e'</b>s <b>u</b>s<b>ua</b>l<b>l</b>y o<b>n</b>l<b>y</b> <b>on</b>e t<b>h</b>in<b>g</b> <b>t</b>hat y<b>o</b>u <b>ca</b>n do<b>.</b> <b>G</b>o <b>th</b>r<b>ou</b>gh h<b>is</b> cl<b>o</b>t<b>h</b>es and look for loose change.</p>
```

If you translate all the ones and zeros correctly, you get the follow binary values that decode into the numbers that relate to the letters below:

|binary|byte|letter|
|:---:| :---: | :---: |
| 01101001 | 105 | i |
| 01110100 | 116 | t |
| 00101101 | 45 | - |
| 01110111 | 119 | w |
| 01101111 | 111 | o |
| 01110101 | 117 | u |
| 01101100 | 108 | l |
| 01100100 | 100 | d |
| 00101101 | 45 | - |
| 01110100 | 116 | t |
| 01100001 | 97 | a |
| 01101011 | 107 | k |
| 01100101 | 101 | e |
| 00101101 | 45 | - |
| 01100001 | 97 | a |
| 00101101 | 45 | - |
| 01101101 | 109 | m |
| 01101001 | 105 | i |
| 01110010 | 114 | r |
| 01100001 | 97 | a |
| 01100011 | 99 | c |
| 01101100 | 108 | l |
| 01100101 | 101 | e |
| 00000000 | 0 | 00 |
| 00000000 | 0 | 00 |
| 00000000 | 0 | 00 |

[here is a LINQPad query](http://share.linqpad.net/uk63mo.linq) that parses the html for this challenge.