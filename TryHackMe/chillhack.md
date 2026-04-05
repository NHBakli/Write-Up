starting with a nmap 
```
nmap -p- -A chill.thm
```
<img width="716" height="817" alt="image" src="https://github.com/user-attachments/assets/0604c56c-b89f-4354-a324-7bf8eea3f4bc" />


then a gobuster 
```
gobuster dir -u http://chill.thm -w /usr/share/wordlists/dirb/big.txt -x php 
```
<img width="716" height="542" alt="image" src="https://github.com/user-attachments/assets/86f0e018-cbbd-4cdf-97e4-e66e8ec5bd09" />
