# m00nwalk

At first I tried to decode the audio file provided using morse code but that resulted in being inconclusive. The waveform of the audio did not seem to provide any results.

I tried searching for the format the Apollo mission used to transmit images and I found:
![sstv](images/sstv.png)
![sstv space](images/sstvspace.png)

I found this [github repository](https://github.com/colaclanth/sstv) that contained a sstv decoder and used it to decode the audio file.

On decoding, you get the result:
![result](images/result.png)

Rotating the image gives you the flag: `picoCTF{beep_boop_im_in_space}`