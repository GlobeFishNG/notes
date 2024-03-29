---
description: Aria2 and AriaNg
---

# Aria2

{% code title="docker-compose.yml" %}
```yaml
version: "3.8"
services:
  aria2-pro:
    container_name: aria2-pro
    image: p3terx/aria2-pro
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK_SET=022
      - RPC_SECRET=GlobeFish
      - RPC_PORT=6800
      - LISTEN_PORT=6888
      - DISK_CACHE=64M
      - IPV6_MODE=false
      - UPDATE_TRACKERS=true
      - CUSTOM_TRACKER_URL=
      - TZ=Asia/Shanghai
    volumes:
      - /home/globefish/.local/etc/docker-compose/aria2-pro/aria2-config:/config
      - /home/globefish/Downloads/Aria2:/downloads
# If you use host network mode, then no port mapping is required.
# This is your best choice when using IPv6.
    network_mode: bridge
    ports:
      - 6800:6800
      - 6888:6888
      - 6888:6888/udp
    restart: unless-stopped
# Since Aria2 will continue to generate logs, limit the log size to 1M to prevent your hard disk from running out of space.
    logging:
      driver: json-file
      options:
        max-size: 1m
# AriaNg is just a static web page, usually you only need to deploy on a single host.
  ariang:
    container_name: ariang
    image: p3terx/ariang
    network_mode: bridge
    ports:
      - 6880:6880
    restart: unless-stopped
    logging:
      driver: json-file
      options:
        max-size: 1m

```
{% endcode %}

{% code title="aria2.conf" %}
```bash
#
# Copyright (c) 2018-2020 P3TERX <https://p3terx.com>
#
# This is free software, licensed under the MIT License.
# See /LICENSE for more information.
#
# https://github.com/P3TERX/aria2.conf
# File name：aria2.conf
# Description: Awesome Aria2 configuration file
# Version: 2020.08.08
#

## 文件保存设置 ##

# 下载目录。可使用绝对路径或相对路径, 默认: 当前启动位置
dir=/downloads

# 启用磁盘缓存, 0 为禁用缓存，默认:16M
# 路由器或 NAS 等本地设备建议在有足够的内存空闲情况下设置为适当的大小，以减少磁盘 I/O 延长硬盘寿命，但不要超过可用内存空间大小。
disk-cache=64M

# 文件预分配方式, 可选：none, prealloc, trunc, falloc, 默认:prealloc
# 预分配对于机械硬盘可有效降低磁盘碎片、提升磁盘读写性能、延长磁盘寿命。
# 机械硬盘使用 ext4（具有扩展支持），btrfs，xfs 或 NTFS（仅 MinGW 编译版本）等文件系统建议设置为 falloc
# 若无法下载，提示 fallocate failed.cause：Operation not supported 则说明不支持，请设置为 none
# prealloc 分配速度慢, trunc 无实际作用，不推荐使用。
# 固态硬盘不需要预分配，只建议设置为 none ，否则可能会导致双倍文件大小的数据写入，从而影响寿命。
file-allocation=falloc

# 文件预分配大小限制。小于此选项值大小的文件不预分配空间，单位 K 或 M，默认：5M
no-file-allocation-limit=64M

# 断点续传
continue=true

# 始终尝试断点续传，无法断点续传则终止下载，默认：true
always-resume=false

# 不支持断点续传的 URI 数值，当 always-resume=false 时生效。
# 达到这个数值从将头开始下载，值为 0 时所有 URI 不支持断点续传时才从头开始下载。
max-resume-failure-tries=0

# 获取服务器文件时间，默认:false
remote-time=true


## 进度保存设置 ##

# 从会话文件中读取下载任务
input-file=/config/aria2.session

# 在 Aria2 退出时保存`错误/未完成`的下载任务到会话文件
save-session=/config/aria2.session

# 定时保存会话的间隔时间（秒）, 0 为进程正常退出时保存, 默认:0
save-session-interval=1

# 自动保存任务进度的间隔时间（秒），0 为进程正常退出时保存，默认：60
auto-save-interval=1

# 强制保存，即使任务已完成也保存信息到会话文件, 默认:false
# 开启后会在任务完成后保留 .aria2 文件，文件被移除且任务存在的情况下重启后会重新下载。
# 关闭后已完成的任务列表会在重启后清空。
force-save=false


## 下载连接设置 ##

# 文件未找到重试次数，默认:0 (禁用)
# 重试时同时会记录重试次数，所以也需要设置 max-tries 这个选项
max-file-not-found=10

# 最大尝试次数，0 表示无限，默认:5
max-tries=0

# 重试等待时间（秒）, 默认:0 (禁用)
retry-wait=10

# 连接超时时间（秒）。默认：60
connect-timeout=10

# 超时时间（秒）。默认：60
timeout=10

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=5

# 单服务器最大连接线程数, 任务添加时可指定, 默认:1
# 最大值为 16 (增强版无限制), 且受限于单任务最大连接线程数(split)所设定的值。
max-connection-per-server=32

# 单任务最大连接线程数, 任务添加时可指定, 默认:5
split=64

# 文件最小分段大小, 添加时可指定, 取值范围 1M-1024M (增强版最小值为 1K), 默认:20M
# 比如此项值为 10M, 当文件为 20MB 会分成两段并使用两个来源下载, 文件为 15MB 则只使用一个来源下载。
# 理论上值越小使用下载分段就越多，所能获得的实际线程数就越大，下载速度就越快，但受限于所下载文件服务器的策略。
min-split-size=4M

# HTTP/FTP 下载分片大小，所有分割都必须是此项值的倍数，最小值为 1M (增强版为 1K)，默认：1M
piece-length=1M

# 允许分片大小变化。默认：false
# false：当分片大小与控制文件中的不同时将会中止下载
# true：丢失部分下载进度继续下载
allow-piece-length-change=true

# 最低下载速度限制。当下载速度低于或等于此选项的值时关闭连接（增强版本为重连），此选项与 BT 下载无关。单位 K 或 M ，默认：0 (无限制)
lowest-speed-limit=0

# 全局最大下载速度限制, 运行时可修改, 默认：0 (无限制)
max-overall-download-limit=0

# 单任务下载速度限制, 默认：0 (无限制)
max-download-limit=0

# 禁用 IPv6, 默认:false
disable-ipv6=true

# GZip 支持，默认:false
http-accept-gzip=true

# URI 复用，默认: true
reuse-uri=false

# 禁用 netrc 支持，默认:false
no-netrc=true

# 允许覆盖，当相关控制文件(.aria2)不存在时从头开始重新下载。默认:false
allow-overwrite=false

# 文件自动重命名，此选项仅在 HTTP(S)/FTP 下载中有效。新文件名在名称之后扩展名之前加上一个点和一个数字（1..9999）。默认:true
auto-file-renaming=true

# 使用 UTF-8 处理 Content-Disposition ，默认:false
content-disposition-default-utf8=true

# 最低 TLS 版本，可选：TLSv1.1、TLSv1.2、TLSv1.3 默认:TLSv1.2
#min-tls-version=TLSv1.2


## BT/PT 下载设置 ##

# BT 监听端口(TCP), 默认:6881-6999
listen-port=6888

# DHT 网络监听端口(UDP), 默认:6881-6999
dht-listen-port=6888

# 启用 IPv4 DHT 功能, PT 下载(私有种子)会自动禁用, 默认:true
enable-dht=true

# 启用 IPv6 DHT 功能, PT 下载(私有种子)会自动禁用，默认:false
# 在没有 IPv6 支持的环境开启可能会导致 DHT 功能异常
enable-dht6=false

# IPv4 DHT 文件路径，默认：$HOME/.aria2/dht.dat
dht-file-path=/config/dht.dat

# IPv6 DHT 文件路径，默认：$HOME/.aria2/dht6.dat
dht-file-path6=/config/dht6.dat

# IPv4 DHT 网络引导节点
dht-entry-point=dht.transmissionbt.com:6881

# IPv6 DHT 网络引导节点
dht-entry-point6=dht.transmissionbt.com:6881

# 本地节点查找, PT 下载(私有种子)会自动禁用 默认:false
bt-enable-lpd=true

# 启用节点交换, PT 下载(私有种子)会自动禁用, 默认:true
enable-peer-exchange=true

# 单个种子最大连接数，0为不限制，默认:55
bt-max-peers=0

# 期望下载速度。BT 下载速度低于此选项设置的值时临时提高连接数来获得更快的下载速度，单位 K 或 M 。默认:50K
bt-request-peer-speed-limit=10M

# 全局最大上传速度限制, 运行时可修改, 默认:0 (无限制)
# 设置过低可能影响 BT 下载速度
max-overall-upload-limit=2M

# 单任务上传速度限制, 默认:0 (无限制)
max-upload-limit=0

# 最小分享率。当种子的分享率达到此选项设置的值时停止做种, 0 为一直做种, 默认:1.0
# 强烈建议您将此选项设置为大于等于 1.0
seed-ratio=1.0

# 最小做种时间（分钟）。设置为 0 时将在 BT 任务下载完成后停止做种。
seed-time=0

# 做种前检查文件哈希, 默认:true
bt-hash-check-seed=true

# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=false

# BT tracker 服务器连接超时时间（秒）。默认：60
# 建立连接后，此选项无效，将使用 bt-tracker-timeout 选项的值
bt-tracker-connect-timeout=10

# BT tracker 服务器超时时间（秒）。默认：60
bt-tracker-timeout=10

# BT 服务器连接间隔时间（秒）。默认：0 (自动)
#bt-tracker-interval=0

# BT 下载优先下载文件开头或结尾
bt-prioritize-piece=head=32M,tail=32M

# 保存通过 WebUI(RPC) 上传的种子文件(.torrent)，默认:true
# 所有涉及种子文件保存的选项都建议开启，不保存种子文件有任务丢失的风险。
# 通过 RPC 自定义临时下载目录可能不会保存种子文件。
rpc-save-upload-metadata=true

# 下载种子文件(.torrent)自动开始下载, 默认:true，可选：false|mem
# true：保存种子文件
# false：仅下载种子文件
# mem：将种子保存在内存中
follow-torrent=true

# 种子文件下载完后暂停任务，默认：false
# 在开启 follow-torrent 选项后下载种子文件或磁力会自动开始下载任务进行下载，而同时开启当此选项后会建立相关任务并暂停。
pause-metadata=false

# 保存磁力链接元数据为种子文件(.torrent), 默认:false
bt-save-metadata=true

# 加载已保存的元数据文件(.torrent)，默认:false
bt-load-saved-metadata=true

# 删除 BT 下载任务中未选择文件，默认:false
bt-remove-unselected-file=true

# BT强制加密, 默认: false
# 启用后将拒绝旧的 BT 握手协议并仅使用混淆握手及加密。可以解决部分运营商对 BT 下载的封锁，且有一定的防版权投诉与迅雷吸血效果。
# 此选项相当于后面两个选项(bt-require-crypto=true, bt-min-crypto-level=arc4)的快捷开启方式，但不会修改这两个选项的值。
bt-force-encryption=true

# BT加密需求，默认：false
# 启用后拒绝与旧的 BitTorrent 握手协议(\19BitTorrent protocol)建立连接，始终使用混淆处理握手。
#bt-require-crypto=true

# BT最低加密等级，可选：plain（明文），arc4（加密），默认：plain
#bt-min-crypto-level=arc4

# 分离仅做种任务，默认：false
# 从正在下载的任务中排除已经下载完成且正在做种的任务，并开始等待列表中的下一个任务。
bt-detach-seed-only=true


## 客户端伪装 ##

# 自定义 User Agent
user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4160.0 Safari/537.36 Edg/85.0.537.0

# BT 客户端伪装
# PT 下载需要保持 user-agent 和 peer-agent 两个参数一致
# 部分 PT 站对 Aria2 有特殊封禁机制，客户端伪装不一定有效，且有封禁账号的风险。
#user-agent=qBittorrent/4.2.5
peer-agent=qBittorrent/4.2.5
peer-id-prefix=-qB4250-


## 执行额外命令 ##

# 下载停止后执行的命令
# 从 正在下载 到 删除、错误、完成 时触发。暂停被标记为未开始下载，故与此项无关。
on-download-stop=/config/script/delete.sh

# 下载完成后执行的命令
# 此项未定义则执行 下载停止后执行的命令 (on-download-stop)
on-download-complete=/config/script/clean.sh

# 下载错误后执行的命令
# 此项未定义则执行 下载停止后执行的命令 (on-download-stop)
#on-download-error=

# 下载暂停后执行的命令
#on-download-pause=

# 下载开始后执行的命令
#on-download-start=

# BT 下载完成后执行的命令
#on-bt-download-complete=


## RPC 设置 ##

# 启用 JSON-RPC/XML-RPC 服务器, 默认:false
enable-rpc=true

# 接受所有远程请求, 默认:false
rpc-allow-origin-all=true

# 允许外部访问, 默认:false
rpc-listen-all=true

# RPC 监听端口, 默认:6800
rpc-listen-port=6800

# RPC 密钥
rpc-secret=GlobeFish

# RPC 最大请求大小
rpc-max-request-size=10M

# RPC 服务 SSL/TLS 加密, 默认：false
# 启用加密后必须使用 https 或者 wss 协议连接
# 不推荐开启，建议使用 web server 反向代理，比如 Nginx、Caddy ，灵活性更强。
#rpc-secure=false

# 在 RPC 服务中启用 SSL/TLS 加密时的证书文件(.pem/.crt)
#rpc-certificate=/config/xxx.pem

# 在 RPC 服务中启用 SSL/TLS 加密时的私钥文件(.key)
#rpc-private-key=/config/xxx.key

# 事件轮询方式, 可选：epoll, kqueue, port, poll, select, 不同系统默认值不同
#event-poll=select


## 日志设置 ##

# 日志文件保存路径，忽略或设置为空为不保存，默认：不保存
#log=

# 日志级别，可选 debug, info, notice, warn, error 。默认：debug
#log-level=warn

# 控制台日志级别，可选 debug, info, notice, warn, error ，默认：notice
console-log-level=notice

# 安静模式，禁止在控制台输出日志，默认：false
quiet=false


## 增强扩展设置(非官方) ##

# 仅适用于 myfreeer/aria2-build-msys2 (Windows) 和 P3TERX/aria2-builder (GNU/Linux) 项目所构建的增强版本

# 在服务器返回 HTTP 400 Bad Request 时重试，仅当 retry-wait > 0 时有效，默认 false
retry-on-400=true

# 在服务器返回 HTTP 403 Forbidden 时重试，仅当 retry-wait > 0 时有效，默认 false
retry-on-403=true

# 在服务器返回 HTTP 406 Not Acceptable 时重试，仅当 retry-wait > 0 时有效，默认 false
retry-on-406=true

# 在服务器返回未知状态码时重试，仅当 retry-wait > 0 时有效，默认 false
retry-on-unknown=true


## BitTorrent trackers ##
bt-tracker=http://104.238.198.186:8000/announce,http://104.28.1.30:8080/announce,http://104.28.16.69:80/announce,http://1337.abcvg.info:80/announce,http://156.234.201.18:80/announce,http://176.113.68.67:6961/announce,http://184.105.151.164:6969/announce,http://185.148.3.231:80/announce,http://195.201.31.194:80/announce,http://49.12.113.1:8866/announce,http://5.182.210.186:6969/announce,http://5.2.72.76:2710/announce,http://54.39.179.91:6699/announce,http://5rt.tace.ru:60889/announce,http://60-fps.org:80/bt:80/announce.php,http://78.30.254.12:2710/announce,http://95.107.48.115:80/announce,http://[2001:1b10:1000:8101:0:242:ac11:2]:6969/announce,http://[2001:470:1:189:0:1:2:3]:6969/announce,http://[2a04:ac00:1:3dd8::1:2710]:2710/announce,http://aaa.army:8866/announce,http://all4nothin.net:80/announce.php,http://alltorrents.net:80/bt:80/announce.php,http://anidex.moe:6969/announce,http://atrack.pow7.com:80/announce,http://baibako.tv:80/announce,http://big-boss-tracker.net:80/announce.php,http://bluebird-hd.org:80/announce.php,http://bobbialbano.com:6969/announce,http://bt-tracker.gamexp.ru:2710/announce,http://bt.3dmgame.com:2710/announce,http://bt.3kb.xyz:80/announce,http://bt.ali213.net:8000/announce,http://bt.okmp3.ru:2710/announce,http://bt.unionpeer.org:777/announce,http://bt.zlofenix.org:81/announce,http://bt2.54new.com:8080/announce,http://bttracker.debian.org:6969/announce,http://btx.anifilm.tv:80/announce.php,http://data-bg.net:80/announce.php,http://datascene.net:80/announce.php,http://elitezones.ro:80/announce.php,http://explodie.org:6969/announce,http://finbytes.org:80/announce.php,http://funfile.org:2710/announce,http://grifon.info:80/announce,http://h4.trakx.nibba.trade:80/announce,http://kaztorka.org:80/announce.php,http://masters-tb.com:80/announce.php,http://mediaclub.tv:80/announce,http://milanesitracker.tekcities.com:80/announce,http://milliontorrent.pl:80/announce.php,http://music-torrent.net:2710/announce,http://mvgroup.org:2710/announce,http://nyaa.tracker.wf:7777/announce,http://open.acgnxtracker.com:80/announce,http://open.touki.ru:80/announce,http://open.touki.ru:80/announce.php,http://opentracker.i2p.rocks:6969/announce,http://potuk.com:2710/announce,http://pow7.com:80/announce,http://proaudiotorrents.org:80/announce.php,http://retracker.hotplug.ru:2710/announce,http://retracker.sevstar.net:2710/announce,http://retracker.spark-rostov.ru:80/announce,http://rt.tace.ru:80/announce,http://share.camoe.cn:8080/announce,http://siambit.org:80/announce.php,http://t.acg.rip:6699/announce,http://t.nyaatracker.com:80/announce,http://t.overflow.biz:6969/announce,http://t1.leech.ie:80/announce,http://t1.pow7.com:80/announce,http://t2.pow7.com:80/announce,http://torrent-team.net:80/announce.php,http://torrent.arjlover.net:2710/announce,http://torrent.fedoraproject.org:6969/announce,http://torrent.mp3quran.net:80/announce.php,http://torrent.rus.ec:2710/announce,http://torrentclub.online:54123/announce,http://torrents.linuxmint.com:80/announce.php,http://torrentsmd.com:8080/announce,http://tr.cili001.com:8070/announce,http://tr.kxmp.cf:80/announce,http://tracker-cdn.moeking.me:2095/announce,http://tracker.ali213.net:8080/announce,http://tracker.anonwebz.xyz:8080/announce,http://tracker.birkenwald.de:6969/announce,http://tracker.bittor.pw:1337/announce,http://tracker.bt4g.com:2095/announce,http://tracker.dler.org:6969/announce,http://tracker.dutchtracking.nl:80/announce,http://tracker.fdn.fr:6969/announce,http://tracker.files.fm:6969/announce,http://tracker.funfile.org:2710/announce,http://tracker.gbitt.info:80/announce,http://tracker.gcvchp.com:2710/announce,http://tracker.ipv6tracker.ru:80/announce,http://tracker.kali.org:6969/announce,http://tracker.kamigami.org:2710/announce,http://tracker.lelux.fi:80/announce,http://tracker.minglong.org:8080/announce,http://tracker.moeking.me:6969/announce,http://tracker.noobsubs.net:80/announce,http://tracker.opentrackr.org:1337/announce,http://tracker.pow7.com:80/announce,http://tracker.pussytorrents.org:3000/announce,http://tracker.skyts.cn:6969/announce,http://tracker.skyts.net:6969/announce,http://tracker.sloppyta.co:80/announce,http://tracker.tambovnet.org:80/announce.php,http://tracker.trackerfix.com:80/announce,http://tracker.vraphim.com:6969/announce,http://tracker.zerobytes.xyz:1337/announce,http://tracker.zum.bi:6969/announce,http://tracker1.bt.moack.co.kr:80/announce,http://tracker1.itzmx.com:8080/announce,http://tracker2.dler.org:80/announce,http://tracker2.itzmx.com:6961/announce,http://tracker3.dler.org:2710/announce,http://tracker3.itzmx.com:6961/announce,http://tracker3.itzmx.com:8080/announce,http://vpn.flying-datacenter.de:6969/announce,http://vps02.net.orel.ru:80/announce,http://www.all4nothin.net:80/announce.php,http://www.bitseduce.com:80/announce.php,http://www.biztorrents.com:80/announce.php,http://www.elitezones.ro:80/announce.php,http://www.freerainbowtables.com:6969/announce,http://www.legittorrents.info:80/announce.php,http://www.mvgroup.org:2710/announce,http://www.thetradersden.org/forums/tracker:80/announce.php,http://www.tribalmixes.com:80/announce.php,http://www.tvnihon.com:6969/announce,http://www.worldboxingvideoarchive.com:80/announce.php,http://www.xwt-classics.net:80/announce.php,http://xbtrutor.com:2710/announce,https://104.28.17.69/announce,https://1337.abcvg.info:443/announce,https://aaa.army:8866/announce,https://open.kickasstracker.com:443/announce,https://opentracker.acgnx.se:443/announce,https://torrent.ubuntu.com:443/announce,https://tp.m-team.cc:443/announce.php,https://tr.steins-gate.moe:2096/announce,https://tracker.bt-hash.com:443/announce,https://tracker.coalition.space:443/announce,https://tracker.cyber-hub.net:443/announce,https://tracker.foreverpirates.co:443/announce,https://tracker.gbitt.info:443/announce,https://tracker.imgoingto.icu:443/announce,https://tracker.lelux.fi:443/announce,https://tracker.lilithraws.cf:443/announce,https://tracker.nanoha.org:443/announce,https://tracker.nitrix.me:443/announce,https://tracker.parrotsec.org:443/announce,https://tracker.shittyurl.org:443/announce,https://tracker.sloppyta.co:443/announce,https://tracker.tamersunion.org:443/announce,https://tracker6.lelux.fi:443/announce,https://trakx.herokuapp.com:443/announce,https://w.wwwww.wtf:443/announce,udp://103.196.36.31:6969/announce,udp://103.30.17.23:6969/announce,udp://104.238.159.144:6969/announce,udp://104.238.198.186:8000/announce,udp://104.244.72.77:1337/announce,udp://109.248.43.36:6969/announce,udp://128.199.70.66:5944/announce,udp://134.209.1.127:6969/announce,udp://138.68.171.1:6969/announce,udp://138.68.69.188:6969/announce,udp://144.76.35.202:6969/announce,udp://144.76.82.110:6969/announce,udp://148.251.53.72:6969/announce,udp://151.236.218.182:6969/announce,udp://151.248.118.189:2710/announce,udp://151.80.120.114:2710/announce,udp://159.69.208.124:6969/announce,udp://161.97.119.72:80/announce,udp://168.119.237.9:6969/announce,udp://173.212.223.237:6969/announce,udp://176.113.71.60:6961/announce,udp://176.123.5.238:3391/announce,udp://176.31.101.42:6969/announce,udp://178.159.40.252:6969/announce,udp://178.16.88.250:6969/announce,udp://178.33.73.26:2710/announce,udp://183.222.243.252:8080/announce,udp://185.181.60.67:80/announce,udp://185.8.156.2:6969/announce,udp://185.86.149.205:1337/announce,udp://188.166.71.230:6969/announce,udp://188.40.91.149:6969/announce,udp://193.34.92.5:80/announce,udp://194.182.165.153:6969/announce,udp://195.123.209.147:6969/announce,udp://195.123.209.40:80/announce,udp://195.128.100.150:6969/announce,udp://195.201.94.195:6969/announce,udp://198.100.149.66:6969/announce,udp://198.50.195.216:7777/announce,udp://199.187.121.233:6969/announce,udp://199.195.249.193:1337/announce,udp://199.217.118.72:6969/announce,udp://208.83.20.20:6969/announce,udp://209.141.45.244:1337/announce,udp://212.1.226.176:2710/announce,udp://212.47.227.58:6969/announce,udp://212.83.181.109:6969/announce,udp://213.108.129.160:6969/announce,udp://217.12.210.229:6969/announce,udp://37.1.205.89:2710/announce,udp://37.235.174.46:2710/announce,udp://37.59.48.81:6969/announce,udp://45.33.83.49:6969/announce,udp://45.56.65.82:54123/announce,udp://45.77.100.109:6969/announce,udp://46.101.244.237:6969/announce,udp://46.148.18.250:2710/announce,udp://46.148.18.254:2710/announce,udp://46.4.109.148:6969/announce,udp://47.97.100.153:6969/announce,udp://47.ip-51-68-199.eu:6969/announce,udp://5.206.60.196:6969/announce,udp://5.226.148.20:6969/announce,udp://51.15.2.221:6969/announce,udp://51.15.247.1:6969/announce,udp://51.15.3.74:6969/announce,udp://51.15.40.114:80/announce,udp://51.15.55.204:1337/announce,udp://51.159.64.19:6969/announce,udp://51.254.244.161:6969/announce,udp://51.68.199.47:6969/announce,udp://51.68.34.33:6969/announce,udp://51.77.134.13:6969/announce,udp://51.77.58.98:6969/announce,udp://51.79.81.233:6969/announce,udp://51.81.46.170:6969/announce,udp://52.58.128.163:6969/announce,udp://61.19.251.235:6969/announce,udp://62.138.0.158:6969/announce,udp://62.138.2.239:6969/announce,udp://62.168.229.166:6969/announce,udp://62.210.97.59:1337/announce,udp://6rt.tace.ru:80/announce,udp://78.30.254.12:2710/announce,udp://89.234.156.205:80/announce,udp://9.rarbg.me:2710/announce,udp://9.rarbg.me:2780/announce,udp://9.rarbg.to:2730/announce,udp://91.121.145.207:6969/announce,udp://91.121.2.169:2710/announce,udp://91.149.192.31:6969/announce,udp://91.216.110.52:451/announce,udp://93.158.213.92:1337/announce,udp://94.224.67.24:6969/announce,udp://94.23.183.33:6969/announce,udp://95.217.161.135:6969/announce,udp://95.217.17.195:80/announce,udp://[2001:1b10:1000:8101:0:242:ac11:2]:6969/announce,udp://[2001:470:1:189:0:1:2:3]:6969/announce,udp://[2a03:7220:8083:cd00::1]:451/announce,udp://[2a04:ac00:1:3dd8::1:2710]:2710/announce,udp://[2a04:c44:e00:32e0:4cf:6aff:fe00:aa1]:6969/announce,udp://aaa.army:8866/announce,udp://admin.videoenpoche.info:6969/announce,udp://anidex.moe:6969/announce,udp://blokas.io:6969/announce,udp://bms-hosxp.com:6969/announce,udp://bt.firebit.org:2710/announce,udp://bt.okmp3.ru:2710/announce,udp://bt2.3kb.xyz:6969/announce,udp://bubu.mapfactor.com:6969/announce,udp://cdn-1.gamecoast.org:6969/announce,udp://cdn-2.gamecoast.org:6969/announce,udp://chihaya.toss.li:9696/announce,udp://code2chicken.nl:6969/announce,udp://concen.org:6969/announce,udp://cutiegirl.ru:6969/announce,udp://daveking.com:6969/announce,udp://denis.stalker.upeer.me:1337/announce,udp://denis.stalker.upeer.me:6969/announce,udp://discord.heihachi.pw:6969/announce,udp://dpiui.reedlan.com:6969/announce,udp://drumkitx.com:6969/announce,udp://edu.uifr.ru:6969/announce,udp://engplus.ru:6969/announce,udp://exodus.desync.com:6969/announce,udp://explodie.org:6969/announce,udp://fe.dealclub.de:6969/announce,udp://free-tracker.zooki.xyz:6969/announce,udp://inferno.demonoid.is:3391/announce,udp://ipv4.tracker.harry.lu:80/announce,udp://ipv6.tracker.harry.lu:80/announce,udp://ipv6.tracker.zerobytes.xyz:16661/announce,udp://johnrosen1.com:6969/announce,udp://kanal-4.de:6969/announce,udp://line-net.ru:6969/announce,udp://ln.mtahost.co:6969/announce,udp://mail.realliferpg.de:6969/announce,udp://movies.zsw.ca:6969/announce,udp://mts.tvbit.co:6969/announce,udp://nagios.tks.sumy.ua:80/announce,udp://ns-1.x-fins.com:6969/announce,udp://ns389251.ovh.net:6969/announce,udp://open.demonii.com:1337/announce,udp://open.demonii.si:1337/announce,udp://open.lolicon.eu:7777/announce,udp://open.stealth.si:80/announce,udp://opentor.org:2710/announce,udp://opentracker.i2p.rocks:6969/announce,udp://opentrackr.org:1337/announce,udp://p4p.arenabg.ch:1337/announce,udp://p4p.arenabg.com:1337/announce,udp://peerfect.org:6969/announce,udp://public-tracker.zooki.xyz:6969/announce,udp://public.popcorn-tracker.org:6969/announce,udp://public.publictracker.xyz:6969/announce,udp://qg.lorzl.gq:233/announce,udp://qg.lorzl.gq:2710/announce,udp://retracker.hotplug.ru:2710/announce,udp://retracker.lanta-net.ru:2710/announce,udp://retracker.netbynet.ru:2710/announce,udp://retracker.nts.su:2710/announce,udp://retracker.sevstar.net:2710/announce,udp://sd-161673.dedibox.fr:6969/announce,udp://storage.groupees.com:6969/announce,udp://sugoi.pomf.se:80/announce,udp://t1.leech.ie:1337/announce,udp://t2.leech.ie:1337/announce,udp://t3.leech.ie:1337/announce,udp://teamspeak.value-wolf.org:6969/announce,udp://thetracker.org:80/announce,udp://torrentclub.online:54123/announce,udp://tr.bangumi.moe:6969/announce,udp://tr2.ysagin.top:2710/announce,udp://tracker-udp.gbitt.info:80/announce,udp://tracker.0x.tf:6969/announce,udp://tracker.altrosky.nl:6969/announce,udp://tracker.army:6969/announce,udp://tracker.beeimg.com:6969/announce,udp://tracker.birkenwald.de:6969/announce,udp://tracker.bittor.pw:1337/announce,udp://tracker.btsync.gq:6969/announce,udp://tracker.coppersurfer.tk:6969/announce,udp://tracker.cyberia.is:6969/announce,udp://tracker.dler.org:6969/announce,udp://tracker.ds.is:6969/announce,udp://tracker.e-utp.net:6969/announce,udp://tracker.filemail.com:6969/announce,udp://tracker.flashtorrents.org:6969/announce,udp://tracker.fortu.io:6969/announce,udp://tracker.grepler.com:6969/announce,udp://tracker.kali.org:6969/announce,udp://tracker.kamigami.org:2710/announce,udp://tracker.leechers-paradise.org:6969/announce,udp://tracker.lelux.fi:6969/announce,udp://tracker.moeking.me:6969/announce,udp://tracker.openbittorrent.com:80/announce,udp://tracker.opentrackr.org:1337/announce,udp://tracker.piratepublic.com:1337/announce,udp://tracker.pomf.se:80/announce,udp://tracker.publictracker.xyz:6969/announce,udp://tracker.sbsub.com:2710/announce,udp://tracker.shkinev.me:6969/announce,udp://tracker.sigterm.xyz:6969/announce,udp://tracker.sktorrent.net:6969/announce,udp://tracker.skyts.net:6969/announce,udp://tracker.swateam.org.uk:2710/announce,udp://tracker.teambelgium.net:6969/announce,udp://tracker.tiny-vps.com:6969/announce,udp://tracker.tntvillage.scambioetico.org:2710/announce,udp://tracker.torrent.eu.org:451/announce,udp://tracker.tvunderground.org.ru:3218/announce,udp://tracker.uw0.xyz:6969/announce,udp://tracker.v6speed.org:6969/announce,udp://tracker.zemoj.com:6969/announce,udp://tracker.zerobytes.xyz:1337/announce,udp://tracker0.ufibox.com:6969/announce,udp://tracker1.bt.moack.co.kr:80/announce,udp://tracker1.itzmx.com:8080/announce,udp://tracker2.christianbro.pw:6969/announce,udp://tracker2.dler.org:80/announce,udp://tracker2.itzmx.com:6961/announce,udp://tracker3.itzmx.com:6961/announce,udp://u.wwwww.wtf:1/announce,udp://udp-tracker.shittyurl.org:6969/announce,udp://us-tracker.publictracker.xyz:6969/announce,udp://valakas.rollo.dnsabr.com:2710/announce,udp://vibe.community:6969/announce,udp://www.loushao.net:8080/announce,udp://www.torrent.eu.org:451/announce,udp://zephir.monocul.us:6969/announce,udp://zer0day.ch:1337/announce,udp://zer0day.to:1337/announce

```
{% endcode %}

