##  network-card

### 关键词: 
- network
- manjaro

### 摘要: 
使用```sudo pacman -Syu```更新内核后, ip无法找到```rtw89_8852ce```无线网卡
参考方法: [manjaro_help](https://forum.manjaro.org/t/wifi-device-not-found-after-upgrade/177672/15)
### 情形:
1. 查看当前所有PCI总线上的设备信息

- 使用```ip```命令查看已连接的网卡
```bash
ip addr show
```
output
```bash
❯ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp7s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 40:c2:ba:a0:9d:21 brd ff:ff:ff:ff:ff:ff
    altname enx40c2baa09d21
```
只有loopback(环回), ether(以太网)网卡显示, 没有无线网卡

<details>
    <summary>enp7s0</summary>
        <ul>
	      <li>en： 表示Ethernet(以太网), 有线网卡</li>
          <li>wl: 表示无线网卡, 如wlp3s0</li>
        </ul>
</details>

- 查看PCI总线上的设备信息
```bash
lspci | grep -i network
```
output
```
08:00.0 Network controller: Realtek Semiconductor Co., Ltd. RTL8852CE PCIe 802.11ax Wireless Network Controller (rev 01)
```
显示无线网卡为```RTL8852CE```

2. 更新内核头文件

当在 Linux 上编译某些程序（尤其是驱动模块、虚拟机工具、Docker、NVIDIA 显卡驱动等）时，这些程序需要知道当前系统使用的 内核接口定义, 如: 内核结构体定义, 函数原型, 版本信息, 各种常量和宏
     
```bash
sudo pacman -S linux612-headers
```

`linux612-headers` 的常见场景包括：

---
| 场景 | 示例 |
|------|------|
| 编译无线网卡驱动 | RTL8852CE, RTL8821CU 等 Realtek 驱动 |
| 安装 NVIDIA 显卡驱动 | 尤其是通过 `.run` 文件安装时 |
| 使用 VirtualBox 或 VMware | 安装增强工具（Guest Additions） |
| 手动构建 DKMS 模块 | 如安装 AUR 中的无线驱动包 |
| 开发或调试内核模块 | 自己写 kernel module |
---

3. 重新加载```rtw89_8852ce```内核模块

```bash
sudo modprobe -v rtw89_8852ce;
lsmod | grep rtw;
```
成功重载```rtw89_8852ce```后

使用 ```nmcli``` 查看是否连接无线网卡

```bash
❯ nmcli device wifi list
IN-USE  BSSID              SSID              MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
        50:98:B8:D1:41:54  NJUPT-CMCC        Infra  11    130 Mbit/s  100     ▂▄▆█  --
        50:98:B8:D1:41:53  NJUPT-CHINANET    Infra  11    130 Mbit/s  100     ▂▄▆█  --
        50:98:B8:D1:41:55  NJUPT             Infra  11    130 Mbit/s  100     ▂▄▆█  --
```

<details>
    <summary>备用方案</summary>
        <ul>
	      <li><strong>usb网卡</strong>：接入一个usb网卡</li>
          <li><strong>接入有线网</strong>: ...</li>
        </ul>
</details>