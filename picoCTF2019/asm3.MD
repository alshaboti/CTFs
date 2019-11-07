I used this emulator to run the script [here](https://carlosrafaelgn.com.br/asm86/),
I had to add the parameters 8bytes above the ebp (that's why I push eax twice).

```
mov eax, 0xb7facecd
push eax
mov eax, 0xb7acde91
push eax
mov eax, 0xaeed09cb
push eax
push eax

push   ebp
mov    ebp,esp
xor    eax,eax
mov    ah,BYTE PTR [ebp+0xb]
shl    ax,0x10
sub    al,BYTE PTR [ebp+0xe]
add    ah,BYTE PTR [ebp+0xd]
xor    ax,WORD PTR [ebp+0x12]
nop
pop    ebp
ret  

```
copy and past the above script and run it in the link above, then see the value of register `eax` 

Falg is `0x69ae`
