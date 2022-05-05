# FUCK-GFW
I do not want waste time in GFW again.
## pip
~/.config/pip/pip.conf
```
[global]
proxy=http://localhost:1087
```
注意不支持socks5
### Reference
* https://my.oschina.net/tangshi/blog/699190

## git
### clone with ssh
在 文件 `~/.ssh/config` 后添加下面两行
```bash
Host github.com
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
```

### clone with http
```
git config --global http.proxy http://127.0.0.1:1087
```
建议使用http, 因为socks5 在使用git-lfs时会报错`proxyconnect tcp: dial tcp: lookup socks5: no such host`

### Reference
https://gist.github.com/laispace/666dd7b27e9116faece6

## cargo
Cargo 会依次检查以下位置
1. 环境变量 `CARGO_HTTP_PROXY`
```bash
export CARGO_HTTP_PROXY=http://127.0.0.1:1080
```
2. [任意 `config.toml`](https://doc.rust-lang.org/cargo/reference/config.html#hierarchical-structure) 中的 `http.proxy`
```toml
[http]
proxy = "127.0.0.1:1080"
```
3. 环境变量 `HTTPS_PROXY` & `https_proxy` & `http_proxy`
```bash
export https_proxy=http://127.0.0.1:1080
export http_proxy=http://127.0.0.1:1080
```
`http_proxy` 一般来讲没必要，除非使用基于 HTTP 的 Crate Repository

Cargo 使用 libcurl，故可接受任何符合 [libcurl format](https://everything.curl.dev/usingcurl/proxies) 的地址与协议

( `127.0.0.1:1080` , `http://127.0.0.1:1080`, `socks5://127.0.0.1:1080` ）均可

### Reference
https://doc.rust-lang.org/cargo/reference/config.html#httpproxy

## apt (apt-get)
在 `/etc/apt/apt.conf.d/` 目录下新增 `proxy.conf` 文件，加入：
```
Acquire::http::Proxy "http://127.0.0.1:8080/";
Acquire::https::Proxy "http://127.0.0.1:8080/";
```
注：无法使用Socks5代理。

### Reference
https://askubuntu.com/a/349765/883355

## curl
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

## Maven
%Maven 安装目录%/conf/settings.xml
```xml
  <!-- proxies
   | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
     <proxy>
      <id>proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>127.0.0.1</host>
      <port>1087</port>
    </proxy>
  </proxies>
```

### Reference
[https://maven.apache.org/guides/mini/guide-proxies.html](https://maven.apache.org/guides/mini/guide-proxies.html)

## go get
```
HTTP_PROXY=socks5://localhost:1080 go get
```
测试了下HTTPS_PROXY和ALL_PROXY都不起作用

OR使用[goproxy.io](https://goproxy.io/)

## npm
```
npm config set proxy http://127.0.0.1:1087
npm config set https-proxy http://127.0.0.1:1087
```
用socks5就报错- -

推荐使用yarn，npm是真的慢

### reference
* https://stackoverflow.com/questions/7559648/is-there-a-way-to-make-npm-install-the-command-to-work-behind-proxy

## rustup
```bash
export https_proxy=http://127.0.0.1:1080
```
废物中国电信，不挂代理慢如龟

## yarn
```
yarn config set proxy http://XX
yarn config set https-proxy http://XX
```
不支持socks5

### Reference
https://github.com/yarnpkg/yarn/issues/3418


## gem
~/.gemrc
```
---
# See 'gem help env' for additional options.
http_proxy: http://localhost:1087
```
## reference
* google

## brew
```
ALL_PROXY=socks5://localhost:1080 brew ...
```

## wget
```
use_proxy=yes
http_proxy=127.0.0.1:1087
https_proxy=127.0.0.1:1087
```
~/.wgetrc

### Reference
* https://stackoverflow.com/questions/11211705/how-to-set-proxy-for-wget


## snap
```bash
sudo snap set system proxy.http="http://127.0.0.1:1087"
sudo snap set system proxy.https="http://127.0.0.1:1087"
```

### Reference
https://askubuntu.com/questions/764610/how-to-install-snap-packages-behind-web-proxy-on-ubuntu-16-04#answer-1146047


## docker
```
$ sudo mkdir -p /etc/systemd/system/docker.service.d
$ sudo vim /etc/systemd/system/docker.service.d/proxy.conf

[Service]
Environment="ALL_PROXY=socks5://localhost:1080"

$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

必须是socks5，http不生效
