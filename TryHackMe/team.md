First lets start with a nmap scan

---
<img width="713" height="581" alt="image" src="https://github.com/user-attachments/assets/8f45b099-bf3d-4787-bfd3-6c5d1cb791af" />


---
in the same time a gobuster :

---
<img width="694" height="423" alt="image" src="https://github.com/user-attachments/assets/bfc7f2f7-157a-460c-82ba-9c8570e5aa9c" />


---
in the robots.txt we have a message

```
dale
```

okey so we understand that there is a user called dale 
no more thing interesting

---

lets do a ffuf to explore subdomains 
```
ffuf -u "http://team.thm/" -H "Host: FUZZ.team.thm" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -ac
```

so there is a LFI because of the "?page=" 

---
<img width="1444" height="42" alt="image" src="https://github.com/user-attachments/assets/db79a4f6-357e-49ae-8956-6313d298382f" />

and bingo !

---
<img width="1444" height="42" alt="image" src="https://github.com/user-attachments/assets/862117df-7b86-4ecc-a735-666eb2faf73b" />

the users
```
gyles, dale, ftpuser, ubuntu
```
---
lets check /etc/ssh/sshd_config
the repo where the config ssh live 

---
<img width="1444" height="830" alt="image" src="https://github.com/user-attachments/assets/407f96f8-3fda-4da1-9537-3155a610ea94" />

we have dale's private ssh key !
so we need to remove all the "#" from the key and save it into our kali machine 
once done 
```
chmod 600 id_rsa
```
```
ssh -i id_rsa dale@team.thm
```
---
<img width="729" height="163" alt="image" src="https://github.com/user-attachments/assets/c07a1998-a153-4da6-88c1-026eafe9aa6b" />

congrat we have the user flag !

---

the lets check our permission
```
sudo -l
```
<img width="729" height="163" alt="image" src="https://github.com/user-attachments/assets/055f39f3-fa37-494a-a72d-8ce4d2d629c6" />

-> seems we can execute admin_checks with gyles as a user

---
<img width="729" height="136" alt="image" src="https://github.com/user-attachments/assets/782cbfa3-0cc2-4afc-a3a6-6414a9cb64c3" />

lets type /bin/bash as a entry and congrat u're gyles

---
then when we type 
```
id
```

we can see that gyles has admin as a group 

---

<img width="729" height="37" alt="image" src="https://github.com/user-attachments/assets/a1594573-12a5-455d-9692-c913907a6a7f" />

so lets search for files related to admin group 

---
<img width="713" height="96" alt="image" src="https://github.com/user-attachments/assets/ad835fcf-7300-4d60-b0d0-83cf92c43bb9" />

great, and there's a script.sh

---
<img width="713" height="163" alt="image" src="https://github.com/user-attachments/assets/8cdc9710-d257-449c-be54-f14ecc60e85e" />

so apparently main_backup.sh is executed every minute, and we also know that it excute with root privilege ! lets do a rev shell

---
<img width="713" height="49" alt="image" src="https://github.com/user-attachments/assets/b56e7781-346c-4479-a37c-7865cd21e180" />

wait a minute ! and then great we have the root flag 

---
<img width="713" height="273" alt="image" src="https://github.com/user-attachments/assets/c05ffd58-198b-450d-9cba-1d1dae8fdf24" />



