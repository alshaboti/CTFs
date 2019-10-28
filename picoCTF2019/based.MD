```
#!/usr/bin/python2
from pwn import *
conn = remote('2019shell1.picoctf.com',20836)

#binary to string
l=conn.recvline()
print(l)
while('Please' not in l):
     l=conn.recvline()
     print(l)

print('*',l)
converted = ''.join([ chr(int(b,2)) for b in l.split('the ')[1].split(' as')[0].split()])
print('converted:', converted)
conn.recvuntil('Input:\n')
conn.send(converted+'\n')

# octal to string
l=conn.recvline()
print(l)
while('Please' not in l):
     l=conn.recvline()
     print(l)
print('*',l)
converted = ''.join([ chr(int(b,8)) for b in l.split('the ')[1].split(' as')[0].split()])
print('converted:', converted)
conn.recvuntil('Input:\n')
conn.send(converted+'\n')

# hex to string
l=conn.recvline()
print(l)
while('Please' not in l):
     l=conn.recvline()
     print(l)
print('*',l)
converted = l.split('the ')[1].split(' as')[0].decode('hex')
# python3  bytes.fromhex(str).decode('utf-8')
print('converted:',converted)
conn.recvuntil('Input:\n')
conn.send(converted+'\n')

while('Flag:' not in l):
  l=conn.recvline()
  print(l)

conn.close()
```
