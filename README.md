# V2Ray 安装配置

```text
本文不保证时效性，不保证文内链接的可用性，不保证最终构建服务的稳定性，仅供学习。  
如果你看不懂本文在干什么，请直接寻找第三方服务，切勿浪费时间，已关闭issue板块，感谢。
```

- [V2Ray 安装配置](#v2ray-安装配置)
  - [1. 代理的基本知识](#1-代理的基本知识)
    - [什么是代理服务器？](#什么是代理服务器)
    - [什么时候我们需要代理服务器？](#什么时候我们需要代理服务器)
  - [2. 代理服务器的选择](#2-代理服务器的选择)
    - [网络延迟](#网络延迟)
    - [操作系统](#操作系统)
  - [3. 什么是 V2Ray (Project V)](#3-什么是-v2ray-project-v)
  - [4. 如何在服务器端安装配置 V2Ray 服务](#4-如何在服务器端安装配置-v2ray-服务)
    - [安装 V2Ray 服务](#安装-v2ray-服务)
    - [启用 V2Ray 服务](#启用-v2ray-服务)
    - [配置 V2Ray服务](#配置-v2ray服务)
  - [5. 如何在客户端配置安装 V2RayN / V2RayNG](#5-如何在客户端配置安装-v2rayn--v2rayng)
  - [ping 使用指南](#ping-使用指南)
  - [tracert 使用指南](#tracert-使用指南)
  - [iperf3 使用指南](#iperf3-使用指南)
  - [SSH 使用简要指南](#ssh-使用简要指南)
  - [防火墙端口配置](#防火墙端口配置)
  - [关于IPv6](#关于ipv6)
  - [题外话](#题外话)

## 1. 代理的基本知识

在开始设置之前，推荐你阅读本段内容以了解基本的代理相关知识。

### 什么是代理服务器？

代理服务器（Proxy Server）是一种位于您的设备（如电脑、手机）和互联网之间的中间服务器，它的核心功能是 **“代你办事”** 。  

您可以把它想象成一位中间人或代理人：  

&emsp;没有代理时： 您的设备 → 直接访问网站  
&emsp;使用代理时： 您的设备 → 代理服务器 → 网站  

当您使用代理服务器时，网络流量不会直接发送到目标网站，而是先发送到代理服务器，由代理服务器替你访问网站，然后再将网站的内容返回给您。  

### 什么时候我们需要代理服务器？

| 情况    | 代理服务器的作用 |
|:-------|:----------------|
|保护隐私 |隐藏您的真实 IP 地址|
|突破限制 |访问被地理封锁或网络防火墙屏蔽的内容|
|管理网络 |公司/学校过滤不当内容，控制上网行为|
|加速访问 |通过缓存常用内容节省带宽、提高速度|
|增强安全 |作为内部网络的缓冲层，过滤恶意流量|

## 2. 代理服务器的选择

代理服务器的核心目的是优化网络路径、提升访问效率、增强安全性与隐私性。

### 网络延迟

网络延迟（Ping值）直接决定了代理服务的体验。  
选择的目标不是单纯追求“国外服务器”，而是根据实际应用场景找到延迟最低、最稳定的路径。

```text
明确需求
  优化国内访问（游戏加速、跨网访问）
  海外业务访问（企业办公、学术研究）
  全局网络优化

测试和选择
  Step 1: 获取测试IP：向服务商索取或在其官网找到服务器的测试IP地址。
  Step 2: 本地延迟测试：  
    在本地电脑的命令提示符或PowerShell中，执行 ping <服务器IP>。  
    观察结果：时间=<XX>ms 就是延迟。通常，<50ms 极佳（国内），50ms-150ms 良好（跨境优质线路），>200ms 会有明显延迟感。  
    关键点：不仅要看延迟，还要看稳定性（是否跳ping，即延迟数值波动很大）。  
  Step 3: 路由追踪测试（更高级）：  
    执行 tracert <服务器IP>（Windows）或 traceroute <服务器IP>（Linux/Mac）。  
    观察结果：这条命令会显示数据包从你电脑到服务器经过的每一跳（路由器）。你可以看到在哪里延迟突然增高，从而判断网络拥堵点。  
  Step 4: 带宽测试：  
    使用 iperf3 或服务商提供的测试URL，在测试服务器的上传/下载速度，确保带宽满足你的需求。
```

[ping-使用指南](#ping-使用指南)  
[tracert-使用指南](#tracert-使用指南)  
[iperf3-使用指南](#iperf3-使用指南)

### 操作系统

选择OS主要关乎长期维护成本、安全性和软件兼容性。对于运行代理服务（如v2ray，Xray）来说，主流选择是Linux发行版。

```text
选择建议：
  无脑之选：Ubuntu LTS，Debian或CentOS。它们拥有最广泛的社区支持和文档资源，非常适合部署v2ray等代理服务。
  新手友好：虽然Windows有图形界面，但更推荐学习使用Linux。通过SSH远程管理，一次配置好后几乎不需要再登录，效率更高、更稳定。
  追求轻量：可以考虑更极简的发行版，如Alpine Linux，但其使用方式与Ubuntu/Debian略有不同。
```

本文将以CentOS为例讲解v2ray代理服务的安装与配置，客户端操作系统为Windows/Android，其他系统请参考官方文档进行配置安装。

## 3. 什么是 V2Ray (Project V)

简而言之，一个开源的网络代理工具，如有兴趣请阅读下面文档。  
[Project V](https://www.v2ray.com/)：Project V（V2Ray）官方文档  
[v2ray manual](https://github.com/v2ray/manual)：已过时，请查看 [V2Fly](https://github.com/v2fly/v2fly-github-io)。  
[V2Ray 配置指南（非官方）](https://toutyrater.github.io/)  
[fhs-install-v2ray（一键部署v2ray）](https://github.com/v2fly/fhs-install-v2ray)

## 4. 如何在服务器端安装配置 V2Ray 服务

本文不对如何购买租赁服务器提供任何指导性意见，如有需要请自行研究学习。  
白话版： 如果你都不知道去哪里购买租赁服务器，那么本文不适合你，请使用第三方的服务。
（推荐购买带公网IPv4和[IPv6](#关于ipv6)的服务器）

在此之前，请务必先了解 [SSH](#ssh-使用简要指南) 的用法。
为简化操作，可安装带有用户界面的SSH&SFTP客户端，如 [WinSCP](https://github.com/winscp/winscp)，[FileZilla](https://filezilla-project.org/) 或者 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)。

### 安装 V2Ray 服务

```text
SSH连接到远程服务器后，使用以下一键部署脚本。
  bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
当执行完毕后，看到有 info: V2Ray vX.XX.XX is installed. 的提示信息，即为安装完毕。
```

<img src="./images/V2RayInstalled.png" alt="V2RayInstalledImage" style="width: 70%; height: auto;">

### 启用 V2Ray 服务

```text
经过上述步骤，V2Ray的组件已经成功安装到了服务器上，但是服务还未启用。
继续执行以下两条命令来启用并启动服务。
  systemctl enable v2ray
  systemctl start v2ray
最后执行以下命令来检查服务状态。
  systemctl status v2ray
```

<img src="./images/V2RayServiceCreated.png" alt="V2RayServiceCreated" style="width: 70%; height: auto;">

### 配置 V2Ray服务

[SSH](#ssh-使用简要指南) 章节中也包含SFTP的基本用法，如果不清楚清先查阅。

```text
首先你需要一个UUID作为配置中的clientId，可以使用在线工具生成，也可以使用同目录下的UUIDGenerator.bat来获取（Windows），甚至可以使用00000000-0000-0000-0000-000000000000。
然后更改sampleConfig.json中的clientId为新生成的id，并更改port的值为你想使用的端口号。
并使用SFTP上传该文件到远程服务器上（使用带有UI的SFTP客户端会让这件事更简单）。
当提示 Are you sure you want to continue connecting (yes/no/[fingerprint])?
输入yes，此时yes处于不可见状态，但没关系，然后回车。
当提示 xxxx@aaa.bbb.ccc.ddd's password: 时，输入你的服务器用户的密码。
之后参考图片中各个命令的用法，来进入相应的目录，将配置文件上传到服务器上。

这个地方需要注意的是，不同版本的服务端配置文件地址不一样，请参考systemctl status v2ray的输出中所显示的命令来确定配置文件所在的目录。
例如上图中的： /usr/local/bin/v2ray run -config /usr/local/etc/v2ray/config.json
意思就是当前使用的配置文件是/usr/local/etc/v2ray/config.json这个文件，使用SFTP替换配置文件的时候注意不要误替换其他文件。
```

<img src="./images/SftpUploadConfigFile.png" alt="SftpUploadConfigFile" style="width: 100%; height: auto;">

```text
之后需要配置防火墙，允许通过上面使用的端口。
在SSH终端中执行如下命令，替换下面的端口号几个字为你上面配置文件中使用的端口号。
  firewall-cmd --zone=public --add-port=端口号/tcp --permanent
  firewall-cmd --reload
然后重启v2ray服务
  systemctl restart v2ray
```

<img src="./images/Firewall-cmd.png" alt="Firewall-cmd" style="width: 70%; height: auto;">

## 5. 如何在客户端配置安装 V2RayN / V2RayNG

[V2RayN](https://github.com/2dust/v2rayN)：V2RayN 代码仓库，可在 Release 页获取已编译各桌面系统应用程序。  
[V2RayNG](https://github.com/2dust/v2rayNG)：V2RayNG 代码仓库，可在 Release 页获取已编译 apk 安装包。  
此处仅以 V2RayN 为例，V2RayNG 是 Android 应用，但配置思路与 V2RayN 大致相同。  
**注意，V2Ray 服务对时间同步性要求较高，务必保证客户端与服务端系统时间同步**  
（白话版：如果你自己的系统时间和真实时间差太多，那你自己手动调一下）

```text
下载V2RayN的 v2rayN-windows-64.zip release压缩包并解压，运行 v2rayN.exe。
从左上角添加[VMess]服务器，选择代理核心为XRay，并填写服务器地址，端口，及用户id，并保存。
之后打开设置，参数设置，按需配置是否开机启动，显示日志与实时流量统计并保存。
再之后检查更新，主要需要更新Geo Files（包括GeoSites和GeoIPs）。
最后右键托盘图标，配置代理模式，路由和选择服务器。
代理模式：
  小白请选择自动配置系统代理，这会自动配置你的浏览器和一些应用使用代理。
  进阶用户可以选择清除系统代理，并自己配置各个应用来监听代理端口（位于设置-Core基础设置）。
路由：
  黑名单：所有被大陆屏蔽的网站ip，会使用代理。
  绕过大陆：所有非大陆网址ip，会使用代理。
  全局：所有网址ip都会使用代理。
  可以在设置-路由设置中，自己定义规则，比如使用国内代理进行网址加速，游戏加速之类的。
配置文件：
  选择你自己的使用的配置文件，如果只有一个那就选这个就好了。
最后检查是否可以使用代理打开需要代理的网址，如果成功代理的话，应用日志会显示proxy字样。
```

<img src="./images/V2RayNAddVMessConfig.png" alt="V2RayNAddVMessConfig" style="width: 70%; height: auto;">  
<img src="./images/V2RayNSetttings.png" alt="V2RayNSetttings" style="width: 70%; height: auto;">  
<img src="./images/V2RayNTrayIcon.png" alt="V2RayNTrayIcon" style="width: 30%; height: auto;">  

## ping 使用指南

仅以 Windows 下的 ping 命令为例讲解， 源
 [ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping)。  
关于 Linux 下的 ping 命令，请参考[此处](https://man7.org/linux/man-pages/man8/ping.8.html)。

```text
如下图所示，当使用 ping 命令验证连接情况时，首先检查是否能够连通。  
当出现连接超时时，意味着这台服务器对你当前网络不可达，也就是不可用做代理服务器。（前提是服务端没有禁用ICMP协议, 关于ICMP协议不做展开）
当服务器可达时，会收到如下提示：
  来自 aaa.aaa.aaa.aaa 的回复：字节=bb 时间=cccms TTL=dd
接下来逐词解析，
  来自 aaa.aaa.aaa.aaa 的回复：意味着这台服务器可以ping通，理论上讲可以用作代理服务器，但是还要检查是否能建立TCP/UDP连接。(ICMP是网络层协议，与TCP/UDP不同级)
  字节=bb：可用来测试到服务器链路的MTU，小白可以忽略该字段，只要有返回值就行。（并不推荐去修改服务器的MSS，当你的网络环境变化后，反而会带来性能问题）
  时间=cccms：ccc是网络延迟，理论上讲这个值越低，你的代理服务连接延迟就越低，体验就越好。（只是理论上，实际上影响因素众多）
  TTL=dd：通常ping Linux 服务器的时候，初始值为64，Windows 为128。这个值与初始值的差值意味着你到这个服务器的路由次数，差值越小，网络复杂度越低，理论上讲更优。
```

<img src="./images/PingServer.png" alt="PingServerImage" style="width: 50%; height: auto;">

## tracert 使用指南

[tracert（Windows）](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tracert)
 和 [traceroute（Linux）](https://man7.org/linux/man-pages/man8/traceroute.8.html) 类似，但是并不完全相同，感兴趣请自行阅读文档，此处仅以 tracert 为例。

```text
使用 tracert [-4] <IPv4_IP> 以及 tracert -6 <IPv6_IP> 来观察路由链路。
需要注意的是，在追踪记录中，请求超时可能是因为当前的路由不回复 tracert 的探测请求，但只要后续有返回，则链路仍是正常的。
只要最后显示追踪完成，那么整条网络链路在理论上就没有问题。
（同样的，即是最后一直超时，也并不意味着代理网络连接不能建立，可能存在会丢弃数据包的路由或者你的服务器禁用了ICMP协议，小白可以忽略）

如下面两图显示，IPv4 IP和IPv6 IP的测试结果并不相同，可以根据跃点数和延迟来选择更优的连接。
理论上讲，跃点数越少，延迟越低，链路更优。
```

<img src="./images/Tracert.png" alt="Tracert" style="width: 70%; height: auto;">  
<img src="./images/Tracert6.png" alt="Tracert6" style="width: 70%; height: auto;">

## iperf3 使用指南

[iperf3](https://iperf.fr/) 是一款用来测量最大网络带宽的工具，它可以帮你测试你的客户端到服务端连接的最大带宽。

```text
首先先在服务端安装 iperf3，如上文我使用的是CentOS，那直接执行如下命令即可。
  yum install iperf3
iperf3 在服务端的默认端口是 5201，但是也可以使用 -p 参数指定需要使用的端口。
需要注意的是，务必将要使用的端口添加到防火墙例外中。（请参考后面的端口配置段落）
之后在服务端使用如下命令启动监听。（并不推荐使用 -D 参数，避免有人恶意刷服务器流量）
  iperf3 -s
回到客户端，使用如下两条命令分别测试 
  iperf3.exe -c [IP]
  iperf3.exe -c [IP] -reverse
（如果你看得懂输出，可以直接使用 iperf3.exe -c [IP] -d 来做双向测试）
测试结束后，服务端使用 Ctrl+C 快捷键退出 iperf3，亦可按需再关闭防火墙端口。

以下面两张图为例，
第一张图是测试客户端上传，第二张图是测试客户端下载，需要关注的是 Transfer 字段和 Bitrate 字段。
Transfer 字段显示了发送方发出了多大的数据包，接收方接受了多大的数据包，两方的差值较大的时候，说明当前网络链路的状况不佳，有丢包情况。
Bitrate 字段显示了发送接收速率，可以作为连接速率的参考。
```

<img src="./images/Iperf_Client.png" alt="Iperf_Client" style="width: 70%; height: auto;">  
<img src="./images/Iperf_Client_Reverse.png" alt="Iperf_Client_Reverse" style="width: 70%; height: auto;">  

## SSH 使用简要指南

本文提到的SSH（Secure Shell）仅指 [OpenSSH](https://www.openssh.com/)，如果你的服务器使用其他的 SSH 实现，请参考具体的官方文档。

```text
从 Windows10 开始，OpenSSH客户端已经默认集成，可以直接通过终端命令行来使用。如果你对命令行不熟悉，建议直接使用带UI的连接工具。
通过如下命令来连接到服务器：
  ssh [username]@[host]
之后按提示接受服务器的 public key，并输入密码，连接建立后可以使用终端来执行命令。

如有需要可以配置公钥认证，之后连接无需再使用密码，并且可禁用密码登录，此处不做展开。

在执行完命令，使用 exit 断开连接。 Ctrl + C会被捕获，无法用来断开 ssh 的连接。
```

## 防火墙端口配置

此处仅以 CentOS 为例讲解 [firewalld](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/using-and-configuring-firewalld_configuring-and-managing-networking)
 的用法，关于 [iptables](https://man7.org/linux/man-pages/man8/iptables.8.html)
 和 [ufw](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29) 请参考文档。

```text
此处仅列举几个常用的命令，
查看防火墙服务是否运行：
  systemctl status firewalld
查看当前防火墙配置：
  firewall-cmd --list-all
永久允许指定端口和协议通过（根据需要来设置端口和协议）：
  firewall-cmd --zone=public --add-port=8080/tcp --permanent
永久阻止指定端口和协议通过（根据需要来设置端口和协议）：
  firewall-cmd --zone=public --remove-port=8080/tcp --permanent
在允许/阻止端口协议后，刷新配置使其生效：
  firewall-cmd --reload
```

## 关于IPv6

IPv6是什么网上说的很多了，这里讲一下为什么代理服务器最好支持IPv6。

```text
1. 无论你的网络环境是否支持IPv6，你仍然可以通过代理服务器访问到纯IPv6网站。同理，当你的环境仅支持IPv6时，同样可以使用代理服务器访问到IPv4网站。
2. IPv4和IPv6的路由策略并不相同，你可以通过切换v4/v6地址来获得更好的网络代理体验。
3. 某些情况下，IPv4的地址可能被防火墙阻断，但是IPv6的地址不在过滤名单中，代理服务器仍然可用。
```

可以使用 [Test IPv6](https://test-ipv6.com) 来检查你当前的环境是否支持IPv6。  
亦可使用 ping 命令来探测是否可以访问到一个IPv6的地址。  

## 题外话

本文旨在技术分享，讲解如何使用 v2ray 工具搭建和配置代理服务器。代理服务器作为一种网络中间件，其应用远不止于突破网络限制，它更核心的价值在于：  

```text
1. 提升访问效率与体验：正如游戏加速器通过代理节点优化数据传输路径，降低延迟和丢包，为玩家提供更流畅的游戏体验。
2. 构建安全的本地网络环境：您可以在家庭或公司局域网内部搭建代理，用于安全地转发内网流量、统一管理网络访问策略或进行开发测试。
3. 技术学习与研究：了解代理协议的工作原理、学习网络配置，是网络与运维领域非常有价值的实践技能。
```

v2ray 是一个优秀的开源网络代理工具，其功能强大且灵活。本文的重点是介绍如何利用它来实现各种网络应用场景。
