# Usage

![image-20240802095352261](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802095352261.png)

80

![image-20240802095405041](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802095405041.png)

```
ffuf -u http://usage.htb/ -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 178,0 -t 128 -H "HOST: FUZZ.usage.htb"
```

![image-20240802095708769](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802095708769.png)



测试sql注入

![image-20240802104250730](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240802104250730.png)



​	