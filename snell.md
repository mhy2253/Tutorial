**1. 下载 Snell Server 安装包**

X86_AMD64：

```bash
wget https://dl.nssurge.com/snell/snell-server-v4.0.1-linux-amd64.zip
```

ARM：

```bash
wget https://dl.nssurge.com/snell/snell-server-v4.0.1-linux-aarch64.zip
```

**2. 解压 Snell Server 到指定目录**

```bash
unzip [snell-server-v4.0.1-linux-amd64.zip](http://snell-server-v4.0.1-linux-amd64.zip) -d /usr/local/bin/ && rm ~/[snell-server-v4.0.1-linux-amd64.zip](http://snell-server-v4.0.1-linux-amd64.zip)
```

**3. 赋予服务器权限**

```bash
chmod +x /usr/local/bin/snell-server
```

**4. 创建配置文件**

```bash
mkdir /etc/snell && vim /etc/snell/snell-server.conf
```

写入以下内容：

```
[snell-server]
listen = 0.0.0.0:12321
psk = a1T48yGmETVZytQGBoec
ipv6 = false
```

**5. 配置 systemctl 文件**

```bash
vim /etc/systemd/system/snell-server.service
```

写入以下内容：

```
[Unit]
Description=Snell Proxy Service
After=[network.target](http://network.target)

[Service]
Type=simple
User=root
Group=nogroup
LimitNOFILE=32768
ExecStart=/usr/local/bin/snell-server -c /etc/snell/snell-server.conf
AmbientCapabilities=CAP_NET_BIND_SERVICE
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=snell-server

[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

**6. 开启 snell 服务**

```bash
systemctl enable --now snell-server
```

**7. 查看 Snell 运行状态**

```bash
systemctl status snell-server
```

---

# Shadow-TLS v3 搭建

**1. 下载 Shadow-TLS**

X86_AMD64：

```bash
wget https://github.com/ihciah/shadow-tls/releases/download/v0.2.23/shadow-tls-x86_64-unknown-linux-musl -O /usr/local/bin/shadow-tls
```

ARM：

```bash
wget https://github.com/ihciah/shadow-tls/releases/download/v0.2.23/shadow-tls-aarch64-unknown-linux-musl -O /usr/local/bin/shadow-tls
```

**2. 赋予程序执行权限**

```bash
chmod +x /usr/local/bin/shadow-tls
```

**3. 创建 service 文件**

```bash
vim /etc/systemd/system/shadow-tls.service
```

写入以下内容：

```
[Unit]
Description=Shadow-TLS Server Service
Documentation=man:sstls-server
After=[network-online.target](http://network-online.target)
Wants=[network-online.target](http://network-online.target)

[Service]
Type=simple
ExecStart=/usr/local/bin/shadow-tls --fastopen --v3 server --listen [::]:8443 --server 127.0.0.1:4143 --tls [gateway.icloud.com](http://gateway.icloud.com) --password JsJeWtjiUyVgh0ooqQ
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=shadow-tls
Environment=MONOIO_FORCE_LEGACY_DRIVER=1

[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

**4. 重载 & 启动服务**

```bash
systemctl daemon-reload && systemctl enable --now shadow-tls.service
```

**5. 查看服务状态**

```bash
systemctl status shadow-tls.service
```

## Surge 节点配置写法

```bash
Snell+TLS = snell, vps的ip或域名, 8443, psk=GLk1ff4wuQNCDSqr97WwsHwe8KBjy3S, version=4, shadow-tls-password=JsJeWtjiUyVgh0ooqQ, shadow-tls-sni=[gateway.icloud.com](http://gateway.icloud.com), shadow-tls-version=3
```
