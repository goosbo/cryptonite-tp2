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

# ARMssembly 1

Script Provided:
```assembly
	.arch armv8-a
	.file	"chall_1.c"
	.text
	.align	2
	.global	func
	.type	func, %function
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 83
	str	w0, [sp, 16]
	str	wzr, [sp, 20]
	mov	w0, 3
	str	w0, [sp, 24]
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
	ret
	.size	func, .-func
	.section	.rodata
	.align	3
.LC0:
	.string	"You win!"
	.align	3
.LC1:
	.string	"You Lose :("
	.text
	.align	2
	.global	main
	.type	main, %function
main:
	stp	x29, x30, [sp, -48]!
	add	x29, sp, 0
	str	w0, [x29, 28]
	str	x1, [x29, 16]
	ldr	x0, [x29, 16]
	add	x0, x0, 8
	ldr	x0, [x0]
	bl	atoi
	str	w0, [x29, 44]
	ldr	w0, [x29, 44]
	bl	func
	cmp	w0, 0
	adrp	x0, .LC0
	add	x0, x0, :lo12:.LC0
	bl	puts
	b	.L6
.L4:
	adrp	x0, .LC1
	add	x0, x0, :lo12:.LC1
	bl	puts
.L6:
	nop
	ldp	x29, x30, [sp], 48
	ret
	.size	main, .-main
	.ident	"GCC: (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04) 7.5.0"
	.section	.note.GNU-stack,"",@progbits
```

It is seen that the argument is stored in `w0` before it jumps to the function `func`.
Space is created in the stack using `sub sp, sp, #32` and the argument is stored in the location `sp+12`.
`83`,`0` and `3` are stored in `sp+16`,`sp+20` and `sp+24` respectively(with `w0` acting as a transfer register).

Next, `0` is loaded in `w0` and `83` is loaded in `w1`. The result of the operation `w1<<w0` which is `83<<0` = `83` is stored in `w0` and then stored in memory location `sp+28`
`83` is loaded from `sp+28` to `w1` and `3` is loaded from `sp+24` to `w0`, `w1/w0` is stored in `w0` which is `27`, it is stored in the memory location `sp+28`

The argument is loaded from `sp+12` and stored in `w0` while `27` is loaded into `w1`. The result of `w1-w0` is stored in `w0` and the function returns

We see that `cmp w0,0` after the function checks if `w0` is equal to 0. If this is true it prints "You win!", else it prints "You lose!".
Therefore in the function the result of `w1-w0` should be zero, so the argument much be equal to `w1` -> argument must be `27`

`27` in hex is `0x1b` which represented by 32 bits is `0x0000001b`
Therefore, flag:`picoCTF{0000001b}`