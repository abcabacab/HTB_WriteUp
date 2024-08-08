# Resource

端口扫描

![image-20240805150329669](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805150329669.png)

2222端口运行的openssh版本存在漏洞

https://github.com/asterictnl-lvdw/CVE-2024-6387

```
python CVE-2024-6387.py scan -T 10.10.11.27 -p 2222
```

![image-20240805151546417](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805151546417.png)

运行poc

```
python CVE-2024-6387.py exploit -T 10.10.11.27 -p 2222 -n tun0
```

![image-20240805160819029](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805160819029.png)

没有结果

```
ffuf -u http://itrc.ssg.htb -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 0,154 -t 128 -H 'HOST: FUZZ.itrc.ssg.htb' -v
```

![image-20240805160800546](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805160800546.png)

![image-20240805161319223](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805161319223.png)

子域名枚举没有收获

测试发现有个upload目录

![image-20240805163619619](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805163619619.png)



目录扫描

```
dirsearch -u "http://itrc.ssg.htb/" --exclude-status=400,401,403,404
```

![image-20240805164054630](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805164054630.png)



![image-20240805164153170](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240805164153170.png)



