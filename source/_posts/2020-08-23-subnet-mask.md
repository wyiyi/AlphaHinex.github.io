---
id: subnet-mask
title: "子网掩码是神马"
description: "Subnet Mask"
date: 2020.08.23 10:26
categories:
    - DevOps
tags: [DevOps, Network]
keywords: DevOps, Network, Subnet mask, ifconfig, netmask
cover: /contents/covers/subnet-mask.jpeg
---

在手动配置 IPv4 地址时，需要配置子网掩码，一般会是 `255.255.255.0` 这样的形式，或者在 `ifconfig` 中表示为 `netmask 0xffffff00`。那么什么是子网掩码？子网掩码为什么一般都是这样的呢？

## 先来说说 IP 地址

目前所说的 IP 地址，一般还是指 IPv4 地址。IPv4 是指长度为 32 位（4个字节）的一组数字，为方便记忆及描述，使用十进制表示，并在每个字节之间使用 `.` 间隔（点分十进制，Dotted decimal notation）。

比如 `127.0.0.1` 为 IP 地址的十进制表示，可使用二进制表示为：

```
0111 1111 0000 0000 0000 0000 0000 0001
```

也可使用十六进制表示为：

```
0x7f000001
```

为方便管理，IP 地址被进行了分类：

|类别|High Order Bits|格式|主机地址范围|
|:--|:--|:--|:--|
|A类地址|0    | 网络号（7位）+ 主机号（24位）|1.0.0.0 ~ 127.255.255.255|
|B类地址|10   | 网络号（14位）+ 主机号（16位）|128.0.0.0 ~ 191.255.255.255|
|C类地址|110  | 网络号（21位）+ 主机号（8位）|192.0.0.0 ~ 223.255.255.255|
|D类地址|1110 | 组播地址（28位）|224.0.0.0 ~ 239.255.255.255|
|E类地址|11110| 保留地址 |240.0.0.0 ~ 247.255.255.255|

其中 ABC 三类地址用于 TCP/IP 节点，DE 两类被用于特殊用途。

比如 `127.0.0.1` 的二进制表示首位为 `0`，所以其属于 A 类地址。

## 子网

为了解决标准分类的 IP 地址的有效利用率和路由器工作效率问题，子网（subnet）的概念被提出。

子网的基本思想是：允许将网络划分成多个部分供内部使用，但是对于外部网络仍然像一个网络一样。

标准的 ABC 类将 IP 地址划分为了网络号和主机号两级的层次结构。子网概念的引入，将 IP 地址划分为了三级：网络号 - 子网号 - 主机号。

## 子网掩码

IP 地址被划分为三级之后，一个很现实的问题就是如何从一个 IP 地址中提取出子网号，子网掩码（subnet mask）的概念顺势而生。子网掩码需与 IP 地址结合使用，根据子网掩码判断 IP 地址所在子网号。

子网掩码由连续的 1 和连续的 0 组成，比如 C 类网络地址默认的子网掩码为 `1111 1111 1111 1111 1111 1111 0000 0000`，即我们在 IP 配置中经常使用的 `255.255.255.0`。

Mask 这个词有遮罩的意思，可以将子网掩码也理解为一个遮罩。将二进制的子网掩码遮挡在二进制的 IP 地址上面，子网掩码为 `1` 的位置理解为透明的，可直接看到实际 IP 地址的二进制值，子网掩码为 `0` 的位置不透明，看到的即为子网掩码处的 `0`，得到的新的二进制的值，就代表这个 IP 地址所处的子网。前面这个过程，在二进制运算中被称为 `按位与`。

子网掩码二进制表示的特性，决定可以直接使用 `1` 的位数来描述，比如 `255.255.255.0` 中包含 24 位 `1`，一个 C 类 IP 地址加子网号的信息，可以通过 `192.168.31.192/24` 来表示。

使用子网掩码划分子网后，子网内可以通信，跨子网不能通信，子网间通信应该使用路由器，并正确配置静态路由信息。
