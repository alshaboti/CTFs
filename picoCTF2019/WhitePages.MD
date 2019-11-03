The file contains two type of whitespaces, you can see them in [view-char](https://www.soscisurvey.de/tools/view-chars.php)
online tool. 

```
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                
```
The two whitespace are `\u2003` and `\u0020`. Let's assume that one represents `0` and the other represents `1`, then 
decode this binary into string.

```
#/usr/bin/python3
import re

f = open('whitepages.txt')
content = f.readline()
f.close()

content = content.replace(u'\u2003','0').replace(u'\u0020','1')

n = int(content,2)
decoded = n.to_bytes((n.bit_length()+7)//8,'big').decode()

print(decoded)
```
if you run the code above you get
```

                picoCTF

                SEE PUBLIC RECORDS & BACKGROUND REPORT
                5000 Forbes Ave, Pittsburgh, PA 15213
                picoCTF{not_all_spaces_are_created_equal_0696a7c2dfa36b081b44603b8aa78efd}


```
