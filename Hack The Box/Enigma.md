First i lunch a nmap to scan the IP and know which port is open or not 

 `nmap -Pn -vv 10.129.40.216`

 ```
Starting Nmap 7.93 ( https://nmap.org ) at 2026-07-04 19:41 CEST
Initiating Parallel DNS resolution of 1 host. at 19:41
Completed Parallel DNS resolution of 1 host. at 19:41, 0.00s elapsed
Initiating SYN Stealth Scan at 19:41
Scanning 10.129.40.216 [1000 ports]
Discovered open port 111/tcp on 10.129.40.216
Discovered open port 995/tcp on 10.129.40.216
Discovered open port 143/tcp on 10.129.40.216
Discovered open port 993/tcp on 10.129.40.216
Discovered open port 110/tcp on 10.129.40.216
Discovered open port 80/tcp on 10.129.40.216
Discovered open port 22/tcp on 10.129.40.216
Discovered open port 2049/tcp on 10.129.40.216
Completed SYN Stealth Scan at 19:41, 0.26s elapsed (1000 total ports)
Nmap scan report for 10.129.40.216
Host is up, received user-set (0.015s latency).
Scanned at 2026-07-04 19:41:41 CEST for 0s
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
80/tcp   open  http    syn-ack ttl 63
110/tcp  open  pop3    syn-ack ttl 63
111/tcp  open  rpcbind syn-ack ttl 63
143/tcp  open  imap    syn-ack ttl 63
993/tcp  open  imaps   syn-ack ttl 63
995/tcp  open  pop3s   syn-ack ttl 63
2049/tcp open  nfs     syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.30 seconds
           Raw packets sent: 1000 (44.000KB) | Rcvd: 1000 (40.032KB)
```

We have a website so i gonna launch a gobuster to see which pages exists and which is accessible

`gobuster dir -u http://enigma.htb/ -w /usr/share/seclists/Discovery/Web-Content/common.txt`
```===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://enigma.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 31133]
Progress: 4750 / 4750 (100.00%)
===============================================================
Finished
===============================================================
```

Nothing very interesting i going to try with another seclist 

`gobuster dir -u http://enigma.htb/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt`
```
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://enigma.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
Progress: 29999 / 29999 (100.00%)
===============================================================
Finished
===============================================================
```

We found nothing to with this seclist. I think we should look they differents port with nmap and investing this


