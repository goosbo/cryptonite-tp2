# freakada 3301

I opened the given image on notepad to find:
![freakada image](https://imgur.com/a/wVxpXgF)

`ZIT HQZI OL GWLEXKTR, WXZ ZIT ZKXZI SOTL OF ZIT LIQRGVL. LTTA ZIT HQZZTKFL. QDGFU ZIT EIQGL, Q WTQEGF QVQOZL: izzhl://woz.sn/yktqatlztof0. GFSN ZIT VGKZIN VOSS LTT ZIT XFLTTF. ZIT PGXKFTN IQL PXLZ WTUXF`

This seemed to be encoded text and I suspected it to be a form of monoalphabetic substitution cipher.

On using the monoalphabetic substitution cipher tool of dcode.fr, I got the output:
```
THE PATH IS OBSCURED, BUT THE TRUTH LIES IN THE SHADOWS. SEEK THE PATTERNS. AMONG THE CHAOS, A BEACON AWAITS: HTTPS://BIT.LY/FREAKESTEIN0. ONLY THE WORTHY WILL SEE THE UNSEEN. THE JOURNEY HAS JUST BEGUN
```

Since bit.ly links are case sensitive and I didn't know if the last character `0` was decoded correctly, I played around with it a bit until I got the right link: `https://bit.ly/freakestein0`

This lead to a protondrive link with an image called `ducky.jpg`
![ducky.png](https://imgur.com/a/s75i0iC))

On running `binwalk -e ducky.jpg`, I find a `secret.txt` link with a github user link `https://github.com/freakada-3301`
The user contained a repository `Salvation-in-Decay`. It had three files `Code of Decay`, `Whispers of the forgotten` and `README.md`. Both `Code of Decay` and `Whispers of the forgotten` had text in alien language which decoded to `SOMEHOW FORGOTTEN` and `WE WERE HERE` respectively which seemed to a dead end.

The `README.md` file contained the text `1sk3vr3d_qw5yvvc}` which appeared to be an encoded version of the end of the flag.
On further analysing of the commit history, the texts that used to be contained in `Code of Decay` and `Whispers of the forgotten` looked like a book cipher.
```
In the ruins of time, where decay whispers loud,
A species once scorned rose from the crowd.
Hungry for hope, though their ways seemed absurd,
They danced in the dark, defying the word.
How their stench carried tales of despair,
Yet in their odd rituals, a spark flared there.
The truth lay hidden in filth and grime,
As their wisdom evolved through ages of time.
They thrived where others could only fade,
With courage their crude civilization was made.
Putrid and proud, they stood undeterred,
Singing to stars with each guttural word.
A sign appeared, the days ticking by,
Seven strange omens lit up the sky.
The intellect buried in graveyard lore,
Held the key to humanity’s door.
They carved their symbols in earth and clay,
Willing the stars to show them the way.
Through odd numbers, the riddle was sung,
"From death comes life," in their guttural tongue.
A graveyard planet, a doomed last chance,
Brought salvation's glimmer in their dance.
The path to rebirth, though steep and gray,
Might just lie in their fetid display.
What’s unseen beneath this grotesque mirth?
The Answer that stirs from the depths of earth.
Symbols align as the stars hold their breath,
Hope reborn in the face of death.
Though shunned at first, these creatures grew,
A journey of worth the cosmos knew.
The 7 signs spoke of a rising tide,
An ascension to truth they couldn’t hide.
Realization came in whispers and cries,
That brilliance sometimes wears a crude disguise.
From 2nd chances and acts that seemed odd,
The beasts became more than a wink of God.
In their filth lay the universe's final quest,
Quest to unearth the future from decay’s cruel jest.
So seek them now, these saviors divine,
For the answers they hold may soon be mine.
```
and the key:
```
16:5:1
15:2:9
40:4:1
33:5:5
36:2:4
1:9:4 
23:4:4
25:5:6
5:3:5 
21:5:2
29:6:7
28:6:1
7:8:1 
18:1:7
30:1:1
16:1:1
30:2:1
31:2:1
33:1:1
35:2:1
38:1:1
11:6:3
```

On decoding the cipher(by assigning the respective numbers to line number, word number and character number), I got the output `htt*sdiscoroggAHj7R2Qd` which looked like a discord server invite link with the text following gg(because discord.gg) being the code. This was correct since it lead to a server called freakada-3301.

After joining the vc(and listening to thick of it for the rest of my time solving the challenge), I noticed a discord app `freakada` in the members tab.
It's bio contained the text `give me my prime, i'll give you my location`, I assumed it symbolized a prime number.

After trying to think of what the prime number could be I noticed that 3301 was prime.
![3301](https://imgur.com/a/6ot7gSz)

After analyzing the image, I noticed that it's dimensions `449 × 503` are also prime numbers. 
![prime products](https://imgur.com/a/zGTfE2C)

This gave me the coords, `13.34508015959565, 74.79629600750295`.

Looking them up on Google Earth, led me to Freaky Backshots Cliff next to Food Court 2. There were recent images attached to the location that had a `freakada-3301` theme.

A couple of those images had QR-codes on them(at first I thought all of them led to different links, but all of them were the same)
![qr code](https://imgur.com/a/ERUO2L7)

The qr code led to a proton drive link with a .wav file called `freakmessage.wav` which contained morse code.

Decoding the morse code gave me the result:
![morse result](https://imgur.com/a/DWwqbSm)

Therefore, the first part of the flag was `nite{4_wannabe_`. I have to admit it took a very long time for me to realise that the output also contained morse code at the bottom so I was stuck for a while. 

After I decoded the morsecode `..-. .-. . . -.- . -.--`, I got the output `FREEKEY`.

This seemed to be the Vignere cipher key for the end of the flag in the Readme.md file from before and I was correct as it decoded to `1nt3rn3t_my5tery}`

Flag: `nite{4_wannabe_1nt3rn3t_my5tery}`