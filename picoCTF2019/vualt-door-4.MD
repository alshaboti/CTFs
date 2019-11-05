This is ascii encoding challeng

Note:  
python2 and python3 interprets octal numbers differently. 
In python2 if a number starts with 0 `zero leading` then that's octat so if you print 015 in python2 you get 13.

In python3 leading zero is not allowed, and octal numbers should starts with `0o`
```
#!/usr/bin/python2

enc =[106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  , 0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,0142, 0131, 0164, 063     , 0163, 0137, 067 , 0141, '1' , 'c' , '8' , 'c' , '6' , '6' , '8' , 'b' ]                                                                                                         
print("picoCTF{"+"".join(map(chr, enc[:24])) + "".join(enc[24:])+"}" )
```
flag is `picoCTF{jU5t_4_bUnCh_0f_bYt3s_7a1c8c668b}`


hence, the above code will not work in python3, we need to change base8 numbers by adding `o` after the leading zero.
```
#!/usr/bin/python3                                                                                            
enc = [106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  ,                                                        
       0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,                                                          
   0o142, 0o131, 0o164, 0o63 , 0o163, 0o137, 0o67 , 0o141,                                                  
     '1' , 'c' , '8' , 'c' , '6' , '6' , '8' , 'b' ,]                                                          
print("picoCTF{"+"".join(map(chr, enc[:24])) + "".join(enc[24:])+"}" ) 
```
