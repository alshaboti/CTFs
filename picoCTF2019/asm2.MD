This challenge is running asembly for and asks what is the value of counter after some iterations. 

Note: 
- Arguments always pushed to the satck before calleing the function.
- First arg is in ebp+0x8, second args in ebp+0xc, the [] to indicate that this addresses from memory.

```
asm2:
# save previouse frame pointer
	<+0>:	push   ebp
# start new function frame 
	<+1>:	mov    ebp,esp
# reserve 16bytes for local variables.
	<+3>:	sub    esp,0x10
# copy the second arg and put it in the local variable position ebp-0x4  
	<+6>:	mov    eax,DWORD PTR [ebp+0xc]
	<+9>:	mov    DWORD PTR [ebp-0x4],eax
# copy the first arg and put it in the local variable position ebp-0x8  
	<+12>:	mov    eax,DWORD PTR [ebp+0x8]
	<+15>:	mov    DWORD PTR [ebp-0x8],eax
# from here we stat the for loop  
	<+18>:	jmp    0x50c <asm2+31>
  # add 1 to 0x24
	<+20>:	add    DWORD PTR [ebp-0x4],0x1
  # add 0xf9 to 0x6
	<+24>:	add    DWORD PTR [ebp-0x8],0xf9
  # if 0x6 is less than or equal to 0x3c75 then jump back to <asm2+20>
	<+31>:	cmp    DWORD PTR [ebp-0x8],0x3c75
	<+38>:	jle    0x501 <asm2+20>
# when the value in [ebp-0x8] is greater than 0x3c75 return [ebp-0x4] (that was 0x24 at the begining)
	<+40>:	mov    eax,DWORD PTR [ebp-0x4]
	<+43>:	leave  
	<+44>:	ret    

```

The stack looks like this at instruction <+18>
```
  +--------------+
  |0x24          |
  +--------------+
  |0x6           |
  +--------------+
  |return pointer|
  +--------------+
  |old ebp       |
  +--------------+
  |0x24          |
  +--------------+
  |0x6           |
  +--------------+
```
Simply it means add `0xf9` to `0x6` and add one to `0x24` until `0x6` is greater than `0x3c75` return the new value of 
`0x24`.

```
# 0x24+how many times should we add to 0x6 to be 0x3c75 + 1 to make it bigger than 0xcc75
python -c 'print (hex(0x24+ int( (0x3c75-0x6)/0xf9 ) +1))'
#0x63
```
