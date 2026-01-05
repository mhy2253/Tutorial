**1. 安装程序**

```bash
wget https://github.com/shadowsocks/shadowsocks-rust/releases/download/v1.22.0/shadowsocks-v1.22.0.aarch64-unknown-linux-gnu.tar.xz && tar -xf shadowsocks-v1.22.0.aarch64-unknown-linux-gnu.tar.xz -C /usr/local/bin/ && chmod +x /usr/local/bin/ssserver && rm shadowsocks-v1.22.0.aarch64-unknown-linux-gnu.tar.xz
```

**2. 创建配置文件**

```bash
mkdir -p /etc/shadowsocks/ && vim /etc/shadowsocks/config.json
```

写入以下内容：

```json
{
    "server": "::",
    "server_port": 22253,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "mode": "tcp_and_udp",
    "password": "eNlo@1g@%6gq7H9Y",
    "timeout": 300,
    "method": "chacha20-ietf-poly1305"
}
```

**3. 创建 service 文件**

```bash
vim /etc/systemd/system/shadowsocks.service
```

写入以下内容：

```
[Unit]
Description=Shadowsocks Server
After=[network.target](http://network.target)

[Service]
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks/config.json
Restart=on-abort

[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

**4. 启动 shadowsocks**

```bash
systemctl daemon-reload && systemctl enable --now shadowsocks
```

**重启 shadowsocks：**

```bash
systemctl restart shadowsocks
```

**查看运行状态：**

```bash
systemctl status shadowsocks
```

**查看日志：**

```bash
journalctl -u shadowsocks -o cat -e
```

**查看实时日志：**

```bash
journalctl -u shadowsocks -o cat -f
```

**卸载 shadowsocks：**

```bash
systemctl disable --now shadowsocks && rm -rf /usr/local/bin/ssserver /etc/shadowsocks /etc/systemd/system/shadowsocks.service
```
