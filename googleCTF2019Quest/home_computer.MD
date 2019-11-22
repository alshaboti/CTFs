```
file 86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
unzip 86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
file family.ntfs
```
We know now that this is an NFTS boot partation, let's mount it and see what is inside. 
```
mkdir ntfs
sudo mount -t ntfs family.ntfs ntfs/
cd ntfs/Users/Family/Documents/
cat credentials.txt
"I keep pictures of my credentials in extended attributes."
```
Now let's look at its extended attributes/alternative streams. In ubuntu you need to install `attr` package to use `getfattr`.
```
#https://manpages.ubuntu.com/manpages/bionic/en/man1/getfattr.1.html
sudo apt install attr
getfattr credentials.txt
getfattr --dump credentials.txt
```
Then let's dump the value to a file, then check file type. 
```
getfattr --only-values -n user:FILE0 credentials.txt > data
file data
```
file type is PNG, so we rename it and open it and we get the flag. 
```
mv data data.png
```
Image contains this flag `CTF{congratsyoufoundmycreds}`
