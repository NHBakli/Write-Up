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

bingo ! we got the ssh to enter the server 

<img width="739" height="398" alt="image" src="https://github.com/user-attachments/assets/e345616a-d00d-4698-ac18-95f2afe2fcd4" />
