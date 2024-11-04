# Custom Encryption

I started out by trying to convert the encrypted text to semi-encrypted text. For this I would have to get the `shared_key`.

Parsing of encrypted data:
```python
a = int(lines[0].split('= ')[1])
b = int(lines[1].split('= ')[1])
cipher = eval(lines[2].split(': ')[1])
```

Since `p` and `g` seemed to be fixed to values `97` and `31` respectively, I could get `u` and `v` with the same `generator` function in the encryption code which interns helps me get the key.

There is a test for a valid key in the encryption code, but since I already have encrypted data I ignored it.

```python
u = generator(g,a,p)
v = generator(g, b, p)

shared_key = generator(v, a, p)
```

With this I retrieve the `shared_key` and hence convert the encrypted text to a semi-encrypted format with:
```python
s = ""
print(shared_key)
for i in cipher:
    s+=chr(int(i/311/shared_key))
```

While reading material on XOR, I read that it was reversable, so at first I just used the `dynamic_xor_encrypt` function in the encryption code assuming it would convert the semi-encrypted text to plain text.
```python
def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    c = []
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
        c.append(ord(encrypted_char))
    return cipher_text,c
```

But I was wrong and I wasn't getting the right output
```
e~r~TAFntdbczmJs#re%pa!uN  1t%#uth
```

After testing with smaller values, I figured out that `plaintext[::-1]` reversed the text. That meant that the semiencrypted text is already reversed, it would reverse it again and not give the right result.

```python
textkey = "trudeau"
tkeylen = len(textkey)
z =''
for i,c in enumerate(s):
    z = chr(ord(c)^ord(textkey[i%tkeylen])) +z
print(z)
```

So I took the simple approach of Xoring each character with the corresponding character of the testkey(which repeats after each of it's character is used) and then concatenating it with `z` such that it would be reversed.

This gave me the output,
```
picoCTF{custom_d2cr0pt6d_751a22dc}
```
which was the right flag.