# import rAnDoM

I was not able to complete this challenge but I spent a decent amount of time on it.

```python
chunks = [bytes_to_long(flag[i:i+4]) for i in range(0, len(flag), 4)]
print(chunks)
yap = ""
for i in chunks:
    rAnDoM.sEeD(i)
    yap += hex(rAnDoM.gEtRanDBitS(32))
    yap += hex(rAnDoM.gEtRanDBitS(32))
    yap += hex(rAnDoM.gEtRanDBitS(32))
    yap += hex(rAnDoM.gEtRanDBitS(32))
    yap += hex(rAnDoM.gEtRanDBitS(32))
    yap += hex(rAnDoM.gEtRanDBitS(32))
```
On analysing the code it is seen that the flag is made into chunks of size 4 bytes each and for each chunk 6 random 32 bit numbers are appended to yap with the seed set to the value of the chunk.

Analysis of `rAnDoM.sEeD()`:
```python
def sEeD(cls, seed) -> None:
        if not isinstance(seed, (type(None), int, float, str, bytes, bytearray)):
            raise TypeError('The only supported seed types are: None,\n'
                            'int, float, str, bytes, and bytearray.')
        cls.current_seed = seed
        random.seed(seed)
```
The function essentially just runs `random.seed()` and is therefore no different.

```python
def gEtRanDBitS(cls, bits : int) -> int:
        cls.sEeD(cls.current_seed)
        num = [random.getrandbits(32) for _ in range(624)][cls.indexes[cls.index % len(cls.indexes)]]
        #print(num)
        cls.index += 1
        #print(int(bin(num)[2:34],2))
        return int(bin(num**(bits // 32))[2:bits+2], 2)
```
Now this function generates the first 624 random numbers of a particular seed and picks the `0,1,2,227,228 and 229th` index every time its called incrementally. 

In the encoding program six random numbers are generated per seed, all the values at these indices in the first 624 random numbers are concatenated to `yap`. Every other function in the `rAnDoM.py` file is irrelevant to the challenge.

After researching a bit I found out that python's random module is deterministic and uses something called the Mersenne Twister. It generates a state with 624 32 bit number and random numbers are extracted from it sequentially(with some alteration) and after every 624 numbers the current state is 'twisted' to achieve the next state. 

To solve this challenge, I needed to extract the seed from the 6 random values I had and then use that seed to reconstruct the flag.
I found many tools and libraries(like RandCrack) that can return the seed if you know 624 32 bit values but I had only 6 so I would not be able to use those.

I started looking at Mersenne twister implementations(specifically MT19937 since that is the one used in python).
This is one such implementation that i referred to:
```python
def __init__(self, seed):
        self.MT = np.zeros(624, dtype=np.uint32)
        self.index = 624
        self.seed(seed)
    
    def seed(self, seed):
        self.MT[0] = seed & 0xffffffff
        for i in range(1, 624):
            self.MT[i] = (0x6c078965 * (self.MT[i-1] ^ (self.MT[i-1] >> 30)) + i) & 0xffffffff
    
    def generate(self):
        if self.index >= 624:
            self.twist()
        
        y = self.MT[self.index]
        y ^= (y >> 11)
        y ^= (y << 7) & 0x9d2c5680
        y ^= (y << 15) & 0xefc60000
        y ^= (y >> 18)
        
        self.index += 1
        return y

    def twist(self):
        for i in range(624):
            y = (self.MT[i] & 0x80000000) + (self.MT[(i + 1) % 624] & 0x7fffffff)
            self.MT[i] = self.MT[(i + 397) % 624] ^ (y >> 1)
            if y % 2 != 0:
                self.MT[i] ^= 0x9908b0df
        self.index = 0
```

The `generate` function is used to tamper with the state value to generate the actual outputed random number. I found out that it is reversable so I could get the actual state values out of the 6 random numbers I have.

While this did not give me much information, I noticed that in the twist function, the i+397th value is used. Now 227+397 wraps around to become 0.
I noticed that I had 3 such pairs with (0,227),(1,228) and (2,229). I also noticed that the index starts with 624 which implies that when you generate the number for the first time, it twists it first(since twisting is done when the index reaches 624). This is done so because the first value of the original state is always the seed.

So I figured because I had a method to untamper the random value and essentially get values from the previous state(which should be the first state since we only twist once on generatiion of random numbers), I should be able to get the seed. But I did enconter some problems. 

All implementations of the MT19937 gave me the seed as the first value of the output state. But when I tried the same with the actual random value it seemed to have a different value as the first value. At first I thought it might have twisted it twice instead of once but I was not able to verify it.

Later I found a [resource](https://www.ambionics.io/blog/php-mt-rand-prediction) on retrieving the seed with the i and i+227th value on the php's implementation of Mersenne twister which is simliar to that of python.

```python
def undo_php_mt_reload(S000, S227):
    # m S000
    # u S227
    # v S228
    X = S000 ^ S227

    # This means the mask was applied, and as such that S227's LSB is 1
    s22X_0 = bv(X, 31)
    # remove mask if present
    if s22X_0:
        X ^= 0x9908b0df

    # Another easy guess
    s227_31 = bv(X, 30)
    # remove bit if present
    if s227_31:
        X ^= 1 << 30

    # We're missing bit 0 and bit 31 here, so we have to try every possibility
    s228_1_30 = (X << 1)
    for s228_0 in range(2):
        for s228_31 in range(2):
            s228 = s228_0 | s228_31 << 31 | s228_1_30

            # Check if the results are consistent with the known bits of s227
            s227 = _undo_php_mt_initialize(s228, 228)
            if bv(s227, 0) != s22X_0:
                continue
            if bv(s227, 31) != s227_31:
                continue

            # Check if the guessed seed yields S000 as its first scrambled state
            rand = undo_php_mt_initialize(s228, 228)
            state = php_mt_initialize(rand)
            php_mt_reload(state)

            if not S000 == state[0]:
                continue

            return rand
    return None
```

This function in the linked resource was similar to what I wanted to implement but gave me wrong outputs(which makes sense since this is php's implementatin on python). 

Essentially, I was not able to implement the reversing of twisting to get the previous state and the fact that I was not able to understand the reason for python to have a different first state first value than the seed made me stuck.

bonus: https://github.com/goosbo/mersenne_twister_rust, I had to make a Mersenne Twister implementation myself after all that time spent :).