### 下载程序

```bash
curl -Lo tuic <https://github.com/EAimTY/tuic/releases/latest/download/tuic-server-1.0.0-x86_64-unknown-linux-gnu> && chmod +x tuic && mv -f tuic /usr/local/bin/
```

### 创建配置文件

创建配置目录并编辑配置文件:

```bash
mkdir /usr/local/etc/tuic && vim /usr/local/etc/tuic/config.json
```

写入以下内容:

```json
{
    "server": "[::]:443",
    "users": {
        "018c9516-8a12-7a17-95ac-4945db4fc9eb": "Gh6ZFxX6R8dyyJ$M"
    },
    "certificate": "/etc/nginx/ssl/fullchain.pem",
    "private_key": "/etc/nginx/ssl/private.key",
    "congestion_control": "bbr",
    "alpn": ["h3"],
    "udp_relay_ipv6": false,
    "zero_rtt_handshake": true,
    "dual_stack": true,
    "auth_timeout": "3s",
    "task_negotiation_timeout": "3s",
    "max_idle_time": "10s",
    "max_external_packet_size": 1500,
    "send_window": 16777216,
    "receive_window": 8388608,
    "gc_interval": "3s",
    "gc_lifetime": "15s",
    "log_level": "info"
}
```

### 创建 systemd 服务

创建服务文件:

```bash
vim /etc/systemd/system/tuic.service
```

写入以下内容:

```
[Unit]
Description=tuic service
Documentation=https://github.com/EAimTY/tuic
After=[network.target](http://network.target) [nss-lookup.target](http://nss-lookup.target)

[Service]
User=root
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_NET_RAW
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE CAP_NET_RAW
ExecStart=/usr/local/bin/tuic --config /usr/local/etc/tuic/config.json
Restart=on-failure
RestartSec=10
LimitNOFILE=infinity

[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

### 服务管理

启动服务:

```bash
systemctl daemon-reload && systemctl enable --now tuic
```

重启服务:

```bash
systemctl restart tuic
```

查看服务状态:

```bash
systemctl status tuic
```

### 日志查看

查看日志:

```bash
journalctl -u tuic -o cat -e
```

查看实时日志:

```bash
journalctl -u tuic -o cat -f
```
