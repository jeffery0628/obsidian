---
Create: 2021年 十二月 11日, 星期六 16:59
tags: 
  - linux

---


# Ubuntu


## 查看网络配置
ifconfig
![[700 Attachments/720 pictures/Pasted image 20211211173613.png]]

其中 wlp4s0 为网卡的名称
inet 192.168.0.114  netmask 255.255.255.0  可以表示成 192.168.0.114/24 -->[[300 Resources/计算机网络/IP地址后面斜杠加具体数字|IP地址后面斜杠加具体数字]]


## 修改配置文件
Ubuntu从17.10开始，放弃在/etc/network/interfaces里面配置IP，改为在/etc/netplan/XX-installer-config.yaml的yaml文件中配置IP地址。

### Ubuntu 20.04

```bash
sudo vim  /etc/netplan/xx-installer-config.yaml
```



```bash
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    wlp4s0:   # 配置的网卡的名称
      addresses: [192.168.0.114/24]   # 配置的静态ip地址和掩码
      dhcp4: false   # 关闭dhcp4
      optional: true
      gateway4: 192.168.0.1 # 网关地址
      nameservers:
        addresses: [114.114.114.114,180.76.76.76]  # DNS服务器地址，多个DNS服务器地址需要用英文逗号分隔开，可不配置
```

> dhcp --> [[300 Resources/计算机网络/DHCP]]
> 网关 -- > [[300 Resources/计算机网络/网关]]

使配置生效
```
sudo netplan apply
```