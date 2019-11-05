This is ascii encoding challeng

```
#!/usr/bin/python2

enc =[106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  , 0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,0142, 0131, 0164, 063     , 0163, 0137, 067 , 0141, '1' , 'c' , '8' , 'c' , '6' , '6' , '8' , 'b' ]                                                                                                         
print("picoCTF{"+"".join(map(chr, enc[:24])) + "".join(enc[24:])+"}" )
```
flag is `picoCTF{jU5t_4_bUnCh_0f_bYt3s_7a1c8c668b}`
