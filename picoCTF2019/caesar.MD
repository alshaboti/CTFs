Caesar brute-force
```
python -c "print('\n'.join([ ''.join([chr( (ord(c)+i-ord('a')) % 26 + ord('a') ) for c in 'rgdhhxcviwtgjqxrdcdydkefyh']) for i in range(26)]))"
```
