# Mihomo Linux 安装指南

---

## 1. 下载二进制可执行文件

```bash
wget https://github.com/MetaCubeX/mihomo/releases/download/v1.19.0/mihomo-linux-arm64-v1.19.0.gz
```

---

## 2. 解压并移动到系统目录

将下载的文件解压，重命名为 `mihomo` 并移动到 `/usr/local/bin/`：

```bash
gzip -kd mihomo-linux-arm64-v1.19.0.gz && mv mihomo-linux-arm64-v1.19.0 /usr/local/bin/mihomo
```

---

## 3. 创建配置文件

```bash
mkdir -p /etc/mihomo && vim /etc/mihomo/config.yaml
```

---

## 4. 创建 systemd 服务文件

新建文件 `/etc/systemd/system/mihomo.service`，写入以下内容：

```ini
[Unit]
Description=mihomo Daemon, Another Clash Kernel.
After=network.target NetworkManager.service systemd-networkd.service iwd.service

[Service]
Type=simple
LimitNPROC=500
LimitNOFILE=1000000
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_RAW CAP_NET_BIND_SERVICE CAP_SYS_TIME CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_DAC_OVERRIDE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_RAW CAP_NET_BIND_SERVICE CAP_SYS_TIME CAP_SYS_PTRACE CAP_DAC_READ_SEARCH CAP_DAC_OVERRIDE
Restart=always
ExecStartPre=/usr/bin/sleep 1s
ExecStart=/usr/local/bin/mihomo -d /etc/mihomo
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
```

---

## 5. 服务管理

### 重新加载 systemd

```bash
systemctl daemon-reload
```

### 启用开机自启

```bash
systemctl enable mihomo
```

### 立即启动

```bash
systemctl start mihomo
```

### 重新加载配置

```bash
systemctl reload mihomo
```

---

## 6. 状态与日志

### 查看运行状态

```bash
systemctl status mihomo
```

### 查看日志（末尾）

```bash
journalctl -u mihomo -o cat -e
```

### 实时跟踪日志

```bash
journalctl -u mihomo -o cat -f
```
