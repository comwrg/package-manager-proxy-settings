# FUCK-GFW
I do not want waste time in GFW again.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**目录**

- [pip](#pip)
- [git](#git)
- [cargo](#cargo)
- [apt (apt-get)](#apt-apt-get)
- [curl](#curl)
- [Gradle](#gradle)
- [Maven](#maven)
- [go get](#go-get)
- [npm](#npm)
- [rustup](#rustup)
- [yarn](#yarn)
- [gem](#gem)
- [brew](#brew)
- [wget](#wget)
- [snap](#snap)
- [docker](#docker)
- [Electron Dev Dependency](#electron-dev-dependency)
- [Visual Studio Code Remote (WSL2)](#visual-studio-code-remote-wsl2)
- [Visual Studio Code Remote (SSH)](#visual-studio-code-remote-ssh)
- [Tips](#tips)
- [Scoop](#scoop)
- [OpenWRT opkg](#openwrt-opkg)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

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
# Mac下
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
# Linux下
ProxyCommand nc --proxy-type socks5 --proxy 127.0.0.1:1080 %h %p
```

注意Linux和Mac下ncat/netcat区别，详见https://unix.stackexchange.com/questions/368155/what-are-the-differences-between-ncat-nc-and-netcat

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
### reference
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

## Electron Dev Dependency
设置环境变量
```
ELECTRON_GET_USE_PROXY=true
GLOBAL_AGENT_HTTPS_PROXY=http://localhost:1080
```
### References
* https://www.electronjs.org/docs/latest/tutorial/installation#proxies
* https://github.com/gajus/global-agent/blob/v2.1.5/README.md#environment-variables

## Visual Studio Code Remote (WSL2)

WSL2环境下可以通过设置`~/.vscode-server/server-env-setup`脚本文件，设置开发环境的环境变量，使用代理。

WSL2内环境访问Win下的代理程序端口代理(例子代码中http代理端口监听17070)，因为子网地址每次启动都不一样，需要动态处理。

新建`~/.vscode-server/server-env-setup`文件，该文件会在VSCode启动WSL环境后被`source`。

```
WSL_HOST=$(sed -n '/^nameserver/p' /etc/resolv.conf | cut -d' ' -f2)
export http_proxy=http://${WSL_HOST}:17070
export https_proxy=$http_proxy
export all_proxy=$http_proxy
```

### References
* https://code.visualstudio.com/docs/remote/wsl

## Visual Studio Code Remote (SSH)

VSCode SSH后的环境不会使用本地界面VSCode内的代理设置，如果SSH主机没有默认网络链接或在墙内，会导致问题。

### SSH主机无网络

需要手动下载vscode 的server端传输部署。详情见链接

  * https://stackoverflow.com/questions/56671520/how-can-i-install-vscode-server-in-linux-offline
  * https://gist.github.com/b01/0a16b6645ab7921b0910603dfb85e4fb

### SSH主机在墙内

虽然文档未提及，但是可以使用WSL模式的方案，配置`~/.vscode-server/server-env-setup`文件设置代理。

SSH主机有代理程序监听在17070端口：

新建`~/.vscode-server/server-env-setup`文件，该文件会在VSCode启动WSL环境后被source。
```
export http_proxy=http://127.0.0.1:17070
export https_proxy=$http_proxy
export all_proxy=$http_proxy
```

### References
* https://code.visualstudio.com/docs/remote/ssh

## Tips
推荐使用Clash,[windows版](https://github.com/Fndroid/clash_for_windows_pkg)，[mac版](https://github.com/yichengchen/clashX)（注意：[ClashX Pro](https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public) 包含TUN功能，ClashX不包含），开启Tun mode可以解决大部分的GFW问题

### Reference
https://docs.cfw.lbyczf.com/contents/tun.html


## Scoop
```bash
scoop config proxy 127.0.0.1:1080
```

### Reference
* https://github.com/ScoopInstaller/Scoop/wiki/Using-Scoop-behind-a-proxy#configuring-scoop-to-use-your-proxy

## OpenWRT opkg
在LUCI面版菜单配置或者/etc/opkg.conf末尾追加
```
option http_proxy http://localhost:1080/
```

### References
https://openwrt.org/docs/guide-user/additional-software/opkg
