I used this emulator to run the script [here](https://carlosrafaelgn.com.br/asm86/),
I had to add 4bytes (push ecx) above the ebp to keep the first arg 8bytes above the ebp.

```
; push arg3
mov eax, 0xb7facecd
push eax
; push arg2
mov eax, 0xb7acde91
push eax
; push arg2
mov eax, 0xaeed09cb
push eax
; emulate the ret address (4bytes)
push   ecx

;------------------
push   ebp
mov    ebp,esp
; zeros eax
xor    eax,eax
; copy one byte then zero it by shifting
mov    ah,BYTE PTR [ebp+0xb]
shl    ax,0x10
; 0-0xac=0x54
sub    al,BYTE PTR [ebp+0xe]
; 0+de=de
add    ah,BYTE PTR [ebp+0xd]
; 0xDE54 XOR 0xB7FA
xor    ax,WORD PTR [ebp+0x12]
; ah value is 0x69ae
nop
pop    ebp
ret   
```
copy and past the above script and run it in the link above, then see the value of register `eax` 

Falg is `0x69ae`
