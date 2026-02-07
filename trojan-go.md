## Nginx 配置

**创建配置文件：**

```
server {
    listen 1234 ssl;
    server_name [example.com](http://example.com);  # 将 [example.com](http://example.com) 替换为你的域名

    ssl_certificate /etc/nginx/ssl/fullchain.pem;  # 替换为你的证书路径
    ssl_certificate_key /etc/nginx/ssl/privkey.key;  # 替换为你的私钥路径

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
```

**测试配置：**

```bash
nginx -t
```

**重载配置：**

```bash
nginx -s reload
```

**重启 nginx：**

```bash
systemctl restart nginx
```

## Trojan-Go 节点搭建

**1. 安装 Trojan-Go**

```bash
wget https://github.com/gfw-report/trojan-go/releases/download/v0.10.10/trojan-go-linux-amd64.zip> && unzip [trojan-go-linux-amd64.zip](http://trojan-go-linux-amd64.zip) -d ./trojan-go && mv trojan-go/trojan-go /usr/local/bin && chmod +x /usr/local/bin/trojan-go && rm -rf trojan-go [trojan-go-linux-amd64.zip](http://trojan-go-linux-amd64.zip)
```

**2. 创建配置文件**

```bash
mkdir /etc/trojan-go && vim /etc/trojan-go/config.json
```

写入以下内容：

```json
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 443,
    "remote_addr": "127.0.0.1",
    "remote_port": 80,
    "password": [
        "xHvse7n9wLy#aBB$"
    ],
    "ssl": {
        "cert": "/etc/nginx/ssl/fullchain.pem",
        "key": "/etc/nginx/ssl/private.key",
        "sni": "[domain.com](http://domain.com)",
        "fallback_port": 1234
    }
}
```

<aside>
⚠️

**注意：** 请务必修改配置中的 `password`、`sni` 域名和证书路径为你自己的实际值。

</aside>

**3. 创建 service 文件**

```bash
vim /etc/systemd/system/trojan-go.service
```

写入以下内容：

```
[Unit]
Description=Trojan-Go - An unidentifiable mechanism that helps you bypass GFW
Documentation=https://p4gefau1t.github.io/trojan-go/
After=network.target nss-lookup.target

[Service]
User=root
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
NoNewPrivileges=true
ExecStart=/usr/local/bin/trojan-go -config /etc/trojan-go/config.json
Restart=on-failure
RestartSec=10s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
```

**4. 开启 Trojan-Go**

```bash
systemctl daemon-reload && systemctl enable --now trojan-go
```

**5. 查看运行状态**

```bash
systemctl status trojan-go
```

**6. 查看日志**

```bash
journalctl -u trojan-go -o cat -e
```

**实时日志：**

```bash
journalctl -u trojan-go -o cat -f
```

**卸载 Trojan-Go：**

```bash
systemctl disable --now trojan-go && rm -rf /usr/local/bin/trojan-go /usr/local/etc/trojan-go /etc/systemd/system/trojan-go.service
```
