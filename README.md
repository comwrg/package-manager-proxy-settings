# FUCK-GFW
I do not want waste time in GFW again.
## pip
### 临时使用
```bash
pip install xxx -i https://pypi.douban.com/simple
```
* 豆瓣            https://pypi.douban.com/simple
* 华中理工大学     https://pypi.hustunique.com/simplesimple
* 山东理工大学     https://pypi.sdutlinux.org/simple
* 中国科学技术大学  https://pypi.mirrors.ustc.edu.cn/simple

### 设为默认
~/.pip/pip.conf
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

### Reference
* http://xujihui1985.github.io/pip/2014/02/18/install-pip-use-mirror
* https://mirrors.tuna.tsinghua.edu.cn/help/pypi/

## git
### clone with ssh
在 文件 `~/.ssh/config` 后添加下面两行
```bash
Host github.com
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
```
### Reference
https://gist.github.com/laispace/666dd7b27e9116faece6

## curl or brew
```bash
socks5 = "127.0.0.1:1080"
```
add to `~/.curlrc`

### Reference
https://www.zhihu.com/question/31360766

## Gradle
这个浪费了好长时间额
~/.gradle/gradle.properties
```
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=1087
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=1087
```
### Reference
https://stackoverflow.com/questions/5991194/gradle-proxy-configuration

## go get
```
https_proxy=127.0.0.1:1087 go get
```

## npm
```
npm config set proxy http://127.0.0.1:1087
npm config set https-proxy http://127.0.0.1:1087
```
## reference
* https://stackoverflow.com/questions/7559648/is-there-a-way-to-make-npm-install-the-command-to-work-behind-proxy

## gem
~/.gemrc
```
---
# See 'gem help env' for additional options.
http_proxy: http://localhost:1087
```
## reference
* google
