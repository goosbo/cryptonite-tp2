# GDB baby step 1

Since the challenge asked for the value at the `eax` register and an executable was provided. I disassembled it with `gdb`(also due to the title of the challenge being a hint).

```
 gdb debugger0_a
 disassemble main
 ```
 Provides the output:
```
Dump of assembler code for function main:
   0x0000000000001129 <+0>:	endbr64 
   0x000000000000112d <+4>:	push   rbp
   0x000000000000112e <+5>:	mov    rbp,rsp
   0x0000000000001131 <+8>:	mov    DWORD PTR [rbp-0x4],edi
   0x0000000000001134 <+11>:	mov    QWORD PTR [rbp-0x10],rsi
   0x0000000000001138 <+15>:	mov    eax,0x86342
   0x000000000000113d <+20>:	pop    rbp
   0x000000000000113e <+21>:	ret 
```
The specific line `mov    eax,0x86342` seems to change the value of the `eax` register. Since `mov a,b` assigns a the value of b, `eax` has the value `0x86342`.

On converting to decimal, you get the value `549698`.

flag: `picoCTF{549698}`

# Vault door 3

The `java` file seems to input the flag and perform some operations on it and compare it to a given String `jU5t_a_sna_3lpm18g947_u_4_m9r54f`.

Now if we reverse these operations on the given String, we could get the `flag` back. Therefore, I wrote a script to do so.
If we analyse the original encryption code:

```java
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
```
We can see that the flag is always 32 characters in length(the part without `picoCTF{` and `}`). The first 8 characters of the flag are not changed.
The next 8 characters are reversed. And for the next 8 characters, the even indexed characters are reversed. All the remaining characters are unchanged.

Therefore the decryption script is:
```cpp
#include <iostream>

using namespace std;

int main(){
    string s = "jU5t_a_sna_3lpm18g947_u_4_m9r54f";
    for(int i = 8; i < 12; i++){
        char t = s[i];
        s[i] = s[23-i];
        s[23-i] = t;
    }
    for(int i = 16; i < 24; i+=2){
        char t = s[i];
        s[i] = s[46-i];
        s[46-i] = t;
    }
    cout<<s<<'\n';
}
```

Output of the script: `jU5t_a_s1mpl3_an4gr4m_4_u_79958f`
On adding the neccessary prefix and suffix, flag:`picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_79958f}`
