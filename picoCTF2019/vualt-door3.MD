Simply reverse the checkPassword function!
```
#!/usr/bin/python3

buf = [c for c in "jU5t_a_sna_3lpm12gb44_u_4_m1r240"]
pswd = ['_' for i in range(32)]
print(pswd)

for i in range(31,16,-2):
   pswd[i] =buf[i]

for i in range(16,32,2):
    pswd[46-i]=buf[i]

for i in range(8,16):
    pswd[23-i]=buf[i]

for i in range(8):
    pswd[i]=buf[i]

print(''.join(pswd))


# flag is picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_41b220}
```
