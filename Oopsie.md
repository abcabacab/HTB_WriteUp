# Oopsie

![image-20240727153814947](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727153814947.png)



查看源码发现后台登录地址

![image-20240727160744106](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727160744106.png)

![image-20240727160807977](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727160807977.png)

login as guest

![image-20240727160938950](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727160938950.png)

上传功能只有super admin用户可以使用s

![image-20240727161023281](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161023281.png)

抓包

![image-20240727161136985](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161136985.png)

![image-20240727161219745](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161219745.png)



尝试爆破admin账户

![image-20240727161306749](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161306749.png)

![image-20240727161337630](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161337630.png)

发现super admin账户，id是30，access id:86575

![image-20240727161554516](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161554516.png)

改包

![image-20240727161758172](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161758172.png)

![image-20240727161816048](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727161816048.png)

上传木马b374k.php

![image-20240727162015719](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727162015719.png)

抓包，需要修改user和role字段（S是打错的）

![image-20240727162203337](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727162203337.png)

dirsearch扫描发现上传目录/uploads,访问并抓包![image-20240727162935553](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727162935553.png)

修改数据包访问提示未找到文件

![image-20240727164049835](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164049835.png)



重新上传一个反弹shell的php文件https://github.com/pentestmonkey/php-reverse-shell

![image-20240727164235025](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164235025.png)

![image-20240727164255430](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164255430.png)

![image-20240727164015277](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164015277.png)

![image-20240727164402228](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164402228.png)

![image-20240727164415879](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164415879.png)



访问

![image-20240727164501063](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164501063.png)

成功反弹shell

![image-20240727164535922](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164535922.png)



查看/etc/passwd文件发现有个可登录用户robert

![image-20240727164901277](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727164901277.png)



进入用户目录，查看user.txt

![image-20240727165644159](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727165644159.png)





在/var/www/html/cdn-cgi/login/db.php文件下找到用户密码

```
M3g4C0rpUs3r!
```

![image-20240727165612839](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727165612839.png)

尝试ssh登录

![image-20240727165936656](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727165936656.png)

robert输入用户组bugtracker,查找这个用户组拥有的文件

![image-20240727170141622](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727170141622.png)

查看文件发现是二进制文件，尝试运行

![image-20240727170500534](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727170500534.png)

程序运行的时候会使用cat命令打开一个文件，尝试环境变量提权

![image-20240727172711307](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727172711307.png)



![image-20240727172834842](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727172834842.png)