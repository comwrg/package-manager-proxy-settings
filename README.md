# package manager proxy settings

如果你的包管理器想直接使用优秀的镜像仓库，请参考这个：[Thanks-Mirror](https://github.com/eryajf/Thanks-Mirror)

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
- [yarn2](#yarn2)
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
- [Chocolatey](#chocolatey)
- [Guix](#guix)
- [FAQ](#faq)

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
# Windows下
ProxyCommand connect -S 127.0.0.1:1086 %h %p
```

注意Linux和Mac下ncat/netcat区别，详见https://unix.stackexchange.com/questions/368155/what-are-the-differences-between-ncat-nc-and-netcat

### clone with http
```
git config --global http.proxy http://127.0.0.1:1087
```

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
如果希望使用 Socks5 代理，则加入：
```
Acquire::http::Proxy "socks5h://127.0.0.1:8080/";
Acquire::https::Proxy "socks5h://127.0.0.1:8080/";
```

### Reference
https://askubuntu.com/a/349765/883355
https://github.com/lanlin/notes/issues/109

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
* 有些包要在 postinstall 阶段下载内容的还需要配置环境变量 https://antfu.me/posts/npm-binary-mirrors

## rustup
```bash
export https_proxy=http://127.0.0.1:1080
```
废物中国电信，不挂代理慢如龟

## yarn
```
yarn config set proxy http://127.0.0.1:1087
yarn config set https-proxy http://127.0.0.1:1087
```
不支持socks5

### Reference
https://github.com/yarnpkg/yarn/issues/3418

## yarn2
[Yarn 2+ - Official](https://yarnpkg.com/)

```sh
yarn config set httpProxy http://127.0.0.1:1087
yarn config set httpsProxy http://127.0.0.1:1087
```
**不支持全局设置**
支持socks5

提示: 这个命令会修改项目目录下的`.yarnrc.yml`文件, 请留意不要把带有如:
```yml
httpsProxy: "socks5://127.0.0.1:1080"
```
的代码提交到仓库, 以免造成麻烦
<details>
<summary>建议使用npm镜像而不是配置使用代理</summary>

```sh
yarn config set npmRegistryServer https://127.0.0.1:1087
```
注意: 此方法不适用于下载yarn官方插件!
yarn的官方插件默认会从GitHub(raw.githubusercontent.com)上下载
您可能依旧需要配置代理
</details>

<details>
  <summary>

   ### Reference
  </summary>

- [yarn doc - httpProxy](https://yarnpkg.com/configuration/yarnrc#httpProxy)
- [yarn doc - httpsProxy](https://yarnpkg.com/configuration/yarnrc#httpsProxy)
</details>

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

## Chocolatey

从0.9.9.9版本开始，choco支持在配置文件显式配置代理。
```
# 设置代理
choco config set proxy http://localhost:8888
# 取消代理
choco config unset proxy
```
除此之外，从0.10.4版本开始，`choco`会自动寻找`http_proxy`和`https_proxy`或者`noproxy`环境变量，通过在命令行临时设置环境变量的方式也可以方便调整`choco`的代理设置。

### Reference
* https://docs.chocolatey.org/en-us/guides/usage/proxy-settings-for-chocolatey#explicit-proxy-settings


## Guix
Guix daemon honors `http_proxy` and `https_proxy` environment variables.

### With Shepherd
```
# Set
herd set-http-proxy guix-daemon http://localhost:1080

# Clean
herd set-http-proxy guix-daemon
```

### Manually or with other service supervisors
```
http_proxy="http://localhost:1080"
env http_proxy="${http_proxy}" https_proxy="${http_proxy}" guix-daemon --build-users-group=guixbuild
```

### Reference
* https://guix.gnu.org/en/manual/devel/en/guix.html#Proxy-Settings
* https://guix.gnu.org/en/manual/devel/en/guix.html#index-guix_002dconfiguration

## FAQ

如果希望在`root`下使用代理，需要使用`visudo`修改`/etc/sudoers`文件
```
Defaults        env_reset
# 新增下行
Defaults        env_keep="http_proxy https_proxy ftp_proxy no_proxy DISPLAY XAUTHORITY"
```

### Reference
* https://www.chenyudong.com/archives/sudo-keep-env.html
