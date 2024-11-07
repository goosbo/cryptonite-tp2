# GDB baby step 1

Well... I was not expecting to solve this module the way I did. So I downloaded the binary and I was going through the reverse engineering resources given. Since the title of the challenge had `GDB` in it I assumed that would be what I would need to use.

Now I disassembled the file using `objdump -d -Mintel debugger0_a` and pipe it into `less` as in the resources as I was messsing around.

On inspecting the output I see the main section,
```
0000000000001129 <main>:
    1129:       f3 0f 1e fa             endbr64 
    112d:       55                      push   rbp
    112e:       48 89 e5                mov    rbp,rsp
    1131:       89 7d fc                mov    DWORD PTR [rbp-0x4],edi
    1134:       48 89 75 f0             mov    QWORD PTR [rbp-0x10],rsi
    1138:       b8 42 63 08 00          mov    eax,0x86342
    113d:       5d                      pop    rbp
    113e:       c3                      ret    
    113f:       90                      nop
```
and specifically, this line `mov    eax,0x86342`.

Since the challenge asked for the value of the `eax` register, I converted `0x86342` to decimal and enclosed it within `picoCTF{}` and it ended up being the flag.

flag: `picoCTF{549698}`

I guess I solved it while I was messing around lol.