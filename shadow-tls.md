## Shadow-Tls v3节点搭建

- **1.下载Shadow-Tls**

X86_AMD64
```
wget https://github.com/ihciah/shadow-tls/releases/download/v0.2.23/shadow-tls-x86_64-unknown-linux-musl -O /usr/local/bin/shadow-tls
```

ARM
```
wget https://github.com/ihciah/shadow-tls/releases/download/v0.2.23/shadow-tls-aarch64-unknown-linux-musl -O /usr/local/bin/shadow-tls
```
- **2.赋予程序执行权限**
```
chmod +x /usr/local/bin/shadow-tls
```
- **3.创建service文件**
```
vim /etc/systemd/system/shadow-tls.service
```
写入以下内容
```
[Unit]
Description=Shadow-TLS Server Service
Documentation=man:sstls-server
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/local/bin/shadow-tls --fastopen --v3 server --listen [::]:8443 --server 127.0.0.1:4143 --tls gateway.icloud.com --password JsJeWtjiUyVgh0ooqQ
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=shadow-tls
Environment=MONOIO_FORCE_LEGACY_DRIVER=1

[Install]
WantedBy=multi-user.target
```
- **4.重载&启动服务**
```
systemctl daemon-reload && systemctl enable --now shadow-tls.service
```
- 5.**查看服务状态**
```
systemctl status shadow-tls.service
```


### Surge节点配置写法
```
Snell+TLS = snell, vps的ip或域名, 8443, psk=GLk1ff4wuQNCDSqr97WwsHwe8KBjy3S, version=4, shadow-tls-password=JsJeWtjiUyVgh0ooqQ, shadow-tls-sni=gateway.icloud.com, shadow-tls-version=3

```
