what is the return value of asm1(0x76) 
Note: 
1- 0x76 will be in $ebp+8 as the first parameter the asm1 funciton. 
2- The return value always is the value of eax when the funciton return. So the caller can just read the eax
after calling the asm and find out what is the return value. 

```
asm1:
	*<+0>:	push   ebp
	*<+1>:	mov    ebp,esp
	*<+3>:	cmp    DWORD PTR [ebp+0x8],0x575    # compare 0x76 and 0x575
	*<+10>:	jg     0x50f <asm1+34>              # will not jump as 0x76 is not greater than0x575
	*<+12>:	cmp    DWORD PTR [ebp+0x8],0x76     # compare 0x76 with the value 0x76
	*<+16>:	jne    0x507 <asm1+26>              # jump if not equal (it is equal, no jump) 
	*<+18>:	mov    eax,DWORD PTR [ebp+0x8]      # copy 0x76 to eax
	* <+21>:	add    eax,0x11                   # add 0x11 to eax => 0x87
	<+24>:	jmp    0x526 <asm1+57>              # unconditional jump to +57 (which is the return) while the value of eax=0x87
	<+26>:	mov    eax,DWORD PTR [ebp+0x8]
	<+29>:	sub    eax,0x11
	<+32>:	jmp    0x526 <asm1+57>
	<+34>:	cmp    DWORD PTR [ebp+0x8],0x9d5
	<+41>:	jne    0x520 <asm1+51>
	<+43>:	mov    eax,DWORD PTR [ebp+0x8]
	<+46>:	sub    eax,0x11
	<+49>:	jmp    0x526 <asm1+57>
	<+51>:	mov    eax,DWORD PTR [ebp+0x8]
	<+54>:	add    eax,0x11
	<+57>:	pop    ebp
	<+58>:	ret    

```

The answer is 0x87
