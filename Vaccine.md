# Vaccine



端口扫描

![image-20240727173914438](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727173914438.png)



ftp匿名登录

![image-20240727174319379](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174319379.png)

发现一个压缩包，下载下来

![image-20240727174307186](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174307186.png)



zip2john导出压缩包hash

![image-20240727174423638](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174423638.png)

john破解得到密码

![image-20240727174457285](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174457285.png)

解压得到一个index.php和style.css

![image-20240727174551943](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174551943.png)

产看index.php发现账号密码

```
admin/md5:2cb42f8734ea607eefed3b70af13bbd3
```

![image-20240727174658655](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174658655.png)

![image-20240727174904825](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727174904825.png)

用hashcat破解的到明文密码qwerty789

![image-20240727175054536](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727175054536.png)

![image-20240727175102912](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727175102912.png)



尝试登录

![image-20240727175253931](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727175253931.png)

![image-20240727175350994](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727175350994.png)

尝试sql注入

![image-20240727175845215](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727175845215.png)

```
meta' union select 1,VERSION(),NULL,NULL,NULL;-- q
```

![image-20240727181236218](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240727181236218.png)