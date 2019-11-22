
link for the file [here](https://storage.googleapis.com/gctf-2019-attachments/768be4f10429f613eb27fa3e3937fe21c7581bdca97d6909e070ab6f7dbf2fbf)
```
unzip 768be4f10429f613eb27fa3e3937fe21c7581bdca97d6909e070ab6f7dbf2fbf
```
You have image with a name of a satallite `osmium`. 
```
chmod +x init_sat
./init_sat
osmium
```
Now if you press `a` it displays config and link for a file, with a base64 data. 
```
VXNlcm5hbWU6IHdpcmVzaGFyay1yb2NrcwpQYXNzd29yZDogc3RhcnQtc25pZmZpbmchCg==
```
when decoded, we got this hint.
``` 
Username: wireshark-rocks
Password: start-sniffing!
```
You can use wireshark or strace to capture all network traffic of this and all forked processes. 
Run it again with `osmium`, and press `a`. You should get the flag `CTF{4efcc72090af28fd33a2118985541f92e793477f}`
```
strace -f -e trace=send,read,recv -s 9999 ./init_sat 
````

