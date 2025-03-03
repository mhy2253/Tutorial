## Tailscale for OpenWrt Deploy

### 1.下载安装程序
```
wget https://linkload.io/me/packages/-/raw/main/tailscale_1.76.1_amd64.tgz && tar -zxvf tailscale_1.76.1_amd64.tgz && mv /tailscale_1.76.1_amd64/tailscale tailscaled /usr/bin 
```
### 2.安装依赖
```
opkg update && opkg install ca-bundle kmod-tunopkg iptables-nft 
```
### 3.赋予程序执行权限
```
chmod +x /usr/bin/tailscale && chmod +x /usr/bin/tailscaled
```
### 4.添加启动脚本
```
vim /etc/init.d/tailscale
```
```
#!/bin/sh /etc/rc.common

# Copyright 2020 Google LLC.
# SPDX-License-Identifier: Apache-2.0

USE_PROCD=1
START=53
STOP=1

start_service() {
  procd_open_instance
  procd_set_param env TS_DEBUG_FIREWALL_MODE=auto
  procd_set_param command /usr/bin/tailscaled

  # Set the port to listen on for incoming VPN packets.
  # Remote nodes will automatically be informed about the new port number,
  # but you might want to configure this in order to set external firewall
  # settings.
  procd_append_param command --port 41641

  # OpenWRT /var is a symlink to /tmp, so write persistent state elsewhere.
  procd_append_param command --state /etc/config/tailscaled.state
  
  # Persist files for TLS cert & Taildrop files
  procd_append_param command --statedir /etc/tailscale/

  procd_set_param respawn
  procd_set_param stdout 1
  procd_set_param stderr 1

  procd_close_instance
}

stop_service() {
  /usr/bin/tailscaled --cleanup
}
```
### 5.赋予脚本可执行权限
```
chmod +x /etc/init.d/tailscale
```
### 6.启动脚本并设置开机自启
```
/etc/init.d/tailscale start && /etc/init.d/tailscale enable
```
> 在OpenWrt→系统→启动项→本地启动脚本中，将原有的Tailscale命令删除（若有），并添加一行：
```
tailscale up --netfilter-mode=off
```
### 7.关联账户
```
tailscale up
```

### 8.网络及防火墙设置

> 1.创建一个新接口，路径：OpenWrt→网络→接口→添加新接口
```
名称：tailscale
协议:不配置协议
设备:tailscale0
```
> 2.创建一个新的防火墙“区域”，路径：OpenWrt→网络→防火墙→区域→添加
```
名称：tailscale
入站数据：接受（默认状态）
出站数据：接受（默认状态）
转发：接受
IP动态伪装：开启
MSS钳制：开启
涵盖的网络：tailscale
允许转发到目标区域：选择你的LAN（和/或其他内部区域，或者如果你计划将此设备用作出口节点，则选择WAN）
允许来自源区域的转发：选择你的LAN（和/或其他内部区域，如果你不想将LAN流量路由到其他tailscale主机，则留空）
点击“保存并应用”。
```

### 9.开启子网路由和出口节点

> 1.开启ip转发

如果您的Linux系统有一个/etc/sysctl.d目录，请使用：
```
echo 'net.ipv4.ip_forward = 1' |  tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' |  tee -a /etc/sysctl.d/99-tailscale.conf
sysctl -p /etc/sysctl.d/99-tailscale.conf
```
否则，请使用：
```
echo 'net.ipv4.ip_forward = 1' |  tee -a /etc/sysctl.conf
echo 'net.ipv6.conf.all.forwarding = 1' | tee -a /etc/sysctl.conf
sysctl -p /etc/sysctl.conf
```

> 2.将设备设置为出口节点和开启子网路由
```
tailscale set --advertise-routes=192.168.1.0/24 --advertise-exit-node --accept-dns=false
```

### 10.重启tailscale,至此就完成安装Tailscale，并完成了基础配置。
```
service tailscale restart
```
