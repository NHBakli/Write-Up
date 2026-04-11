Nmap scan :

<img width="660" height="358" alt="image" src="https://github.com/user-attachments/assets/71ff35d7-fa19-4656-bf71-d0d873f7dab4" />

the gobuster :

<img width="660" height="372" alt="image" src="https://github.com/user-attachments/assets/4d41204b-d0f1-46f4-951b-ac418b52a0cf" />

once the ip address typed in the browser we find the default page of apache2 :

<img width="807" height="748" alt="image" src="https://github.com/user-attachments/assets/29c172d5-767b-43f4-b3d2-9f1fa83c478e" />


looking a the source page we find a comment :

<img width="1107" height="87" alt="image" src="https://github.com/user-attachments/assets/7a3a9da7-cea8-4e13-a60e-4c4ce1e00b42" />

user found :

```
Jessie
```

we explore a bit the sitemap website  
-> image


while we launched a gobuster on http://ctf.thm/sitemap/

<img width="739" height="398" alt="image" src="https://github.com/user-attachments/assets/a34772a5-0d70-4c3d-86c5-997764265954" />

we find a .ssh repo

<img width="1378" height="321" alt="image" src="https://github.com/user-attachments/assets/34d19f98-cee9-43cc-8e29-4da7c93f6a97" />

so we copy to the file id_rsa in our kali attacker machine and give the right privileges

```
chmod 600 id_rsa
```

and access the server

```
ssh -i id_rsa jessie@ip.thm
```

bingo ! we got the ssh to enter the server 

<img width="739" height="398" alt="image" src="https://github.com/user-attachments/assets/e345616a-d00d-4698-ac18-95f2afe2fcd4" />

the user flag is in /Documents :

<img width="746" height="161" alt="image" src="https://github.com/user-attachments/assets/3a789449-4899-4df0-a160-020442710b43" />

---
to the root 

we do a 
```
sudo -l
```
<img width="746" height="144" alt="image" src="https://github.com/user-attachments/assets/de4b273e-8872-462c-aad3-ecc755a63e64" />

go to GTFObins:

<img width="911" height="466" alt="image" src="https://github.com/user-attachments/assets/a231af2f-85ac-4a68-b70b-3c03f05cf261" />

i guess that the root flag may be 'root_flag.txt'

so :

```
sudo wget -i /root/root_flag.txt -o flag.txt
```

<img width="911" height="170" alt="image" src="https://github.com/user-attachments/assets/f6300f33-67fb-41c9-9940-75891445e811" />

Congrat room done !

