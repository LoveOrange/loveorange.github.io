---
title: "使用 V2Ray 进行科学上网"
date: 2020-03-04T11:34:52+08:00
draft: false
categories: ["Thinking Beyond Coding"]
tags: ["vps"]
---

## 前言

### 科学上网简述

随着个人电脑、智能手机的普及，互联网，已经成为我们日常生活中不可或缺的重要组成部分。有人戏称，断网是对一个人最大的惩罚。那么，对于我们每天使用的网络，它是“互联”的吗？

很遗憾，并不是。

古时候，长城（Great Wall）保护着中原大地不受外敌入侵；如今，防火长城（Great Firewall，以下简称GFW）组织者外部网络的“入侵”。虽然GFW阻止了我们访问一些不良网站， 但是同时，一些优秀的国外网站也被拒之门外，不得不说是一个遗憾。

因此，我们需要解锁上网的新姿势，让我们访问被 GFW 屏蔽掉的优秀网站，去认识更广大的世界，这就是科学上网。
<!--more-->

### 人人都需要科学上网吗？

诚然，很多优秀的网站被 GFW 所屏蔽，但是 GFW 内部仍然有许多优秀的网站、资源可供访问，这些网站足以满足普通用户的日常需求，并且大多数网站对于国内的用户都进行了优化，相比墙外的网站拥有更好的体验。因此对于大部分人来说，科学上网不是必备的技能。

但是对于另一部分从事科研、文艺等内容的人来说，GFW 屏蔽掉了很多优秀资源网站，包括一些资料、文档等；同时，很多网站的服务器架设在国外，从国内访问并没有很好地速度。因此对于这部分人来说，科学上网堪称“刚需”。


## 准备工作

### 基本要求
由于大部分的代理服务器运行的都是 Linux 环境，对于想要搭建代理服务器的用户有一定的 Linux 操作能力。

### 了解自己的需求
科学上网的第一步，就是要了解自己的需求。不妨思考以下几个问题：

- 我是否需要科学上网？
- 我经常浏览什么内容？

这些问题会影响到挑选什么样的代理服务器。如果仅仅为了体验下国外的网站，那么代理服务器选一个便宜的即可；如果需要看一些 YouTube 视频，则要保证代理服务器有足够的带宽。

### 挑选代理服务器
提供代理服务器的大小厂商有很多。

如果是第一次搭建代理服务器的话，推荐使用 [Google Cloud Platform](https://cloud.google.com/)（GCP）提供的虚拟机。GCP 的机房很多，而且速度有保障，可以说是目前速度最快的机器之一。但是缺点是价格较高，不过新用户注册会送 $300 和一年的体验。

除了 GCP 之外，[Vultr](https://www.vultr.com/)，[DigitalOcean](https://www.digitalocean.com/)，[BandwagonHOST](https://bandwagonhost.com/) 也是不错的选择，可以根据自身需求选择合适自己的套餐。

### 科学上网方案
目前常用的科学上网工具有 [ShadowSockes](https://github.com/shadowsocks/shadowsocks/tree/master) 和 [V2Ray](https://www.v2ray.com/)，本文仅介绍 V2Ray 的配置方法，二者具体的差异可以自行调研，并选择合适的方案。

## V2Ray 配置

### 服务端配置
#### 安装
V2Ray 的官网上提供了常见操作系统的[安装方式](https://www.v2ray.com/chapter_00/install.html)，以 Linux 为例，使用以下命令可以直接安装：
```shell
bash <(curl -L -s https://install.direct/go.sh)
```

#### 配置
安装好了后，需要对 V2Ray 做一些基本的配置，V2Ray 默认的配置文件在 `/etc/v2ray/config.json`，以下为一份简要的配置，其中端口可以自行配置，id 可以在 [uuid.online](http://www.uuid.online/) 上生成
```json
{
    "inbounds": [{
        "port": 8088,
        "protocol": "vmess",
        "settings": {
            "clients": [{
                "id": "bf6b5b27-b45a-4c40-a8f1-e127c0690b64",
                "alterId": 16,
                "security": "auto",
                "level": 0
            }]
        }
    }],
    "outbounds": [{
        "protocol": "freedom",
        "settings": {}
    }]
}
```

#### 启动
配置好后，可以在后台运行 V2Ray，成功启动后，服务端配置完成。
```shell
nohup /usr/bin/v2ray/v2ray -config /etc/v2ray/config.json > v2ray.log 2>&1 &
```

#### 开启端口
出于安全的考虑，部分服务器默认没有打开 8088 端口，需要手动打开：
```shell
/sbin/iptables -I INPUT -p tcp --dport 8088 -j ACCEPT
```

## 加速
虽然 V2Ray 的服务端已经配置完成，但是服务器默认的配置速度有限，此时可以使用 [BBR](https://github.com/google/bbr) 等技术对代理服务器进行加速。有大神将常用的加速手段集成为一个一键[脚本](https://github.com/google/bbr)，建议使用 BBR Plus 进行加速。

使用加速后，YouTube 1080p 视频还是毫无压力的。
![YouTube Test](/img/2020/wide_world/youtube_test.png)


## 客户端配置
配置完服务端后，客户端也需要进行相应的配置，才能够顺利地进行科学上网。

V2Ray 有多种客户端可供选择，在 V2Ray 的官网中可以查看客户端列表，这里推荐使用 [V2RayN](https://github.com/2dust/v2rayN/releases)

解压之后配置上自己的服务器并启动：
![V2RayN](/img/2020/wide_world/v2rayn.png)

参数设置中，可以修改端口号，建议为 1080，选择 HTTP 代理为 PAC 模式，就可以进行科学上网了：
![V2RayN Config](/img/2020/wide_world/v2rayn_port.png)

如果使用 Chrome 浏览器的话，可以搭配 SwitchyOmega 插件使用，在 proxy 模式中，代理协议设置为 SOCKS5，代理服务器设置为 127.0.0.1，代理端口设置为 V2RayN 中设置的本地监听端口：
![SwitchyOmega](/img/2020/wide_world/switchyomega.png)

欢迎来到更大的世界~
