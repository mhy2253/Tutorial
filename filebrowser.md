## 安装 FileBrowser

使用一键安装脚本：

```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```

---

## 配置 FileBrowser

### 前置准备：放行端口

假设使用 `9000` 端口（或根据实际需要修改）：

**Debian/Ubuntu 系统：**

```bash
iptables -I INPUT -p tcp --dport 9000 -j ACCEPT
```

**CentOS 系统：**

```bash
firewall-cmd --zone=public --add-port=9000/tcp --permanent
firewall-cmd --reload
```

### 配置三步曲

### 1. 初始化数据库

在 `/data/filebrowser.db` 路径下创建数据库文件并初始化配置（路径可自定义）：

```bash
filebrowser -d /data/filebrowser.db config init
```

### 2. 设置基本参数

设置监听地址、端口和语言：

```bash
filebrowser -d /data/filebrowser.db config set --address 0.0.0.0 --port 8080 --locale zh-cn
```

> **注意**：上面命令中端口设置为 `8080`，如果你前面放行的是 `9000` 端口，这里应改为 `--port 9000` 保持一致。
> 

### 3. 添加管理员用户

创建管理员账户（用户名和密码请自行修改）：

```bash
filebrowser -d /data/filebrowser.db users add mhy2253 865671 --perm.admin
```

> **提示**：也可以跳过此步，使用默认的 `admin/admin` 账户，登录后在后台修改。
> 

### 启动测试

启动调试模式测试配置：

```bash
sudo filebrowser -d /data/filebrowser.db
```

通过浏览器访问 [`http://IP:端口`](http://IP:端口)，出现登录界面表示安装成功：

![](https://oooutman.github.io/img/post/FileBrowser/filebrowser_01.jpg)

测试完成后，按 `Ctrl+C` 退出调试模式。

> 如果无法访问，请检查数据库路径、IP、端口配置是否正确，以及端口是否已放行。
> 

---

## 配置 systemd 服务

### 创建服务文件

```bash
sudo vim /etc/systemd/system/filebrowser.service
```

写入以下内容：

```
[Unit]
Description=FileBrowser
After=[network.target](http://network.target)

[Service]
ExecStart=/usr/local/bin/filebrowser/filebrowser -d /data/filebrowser.db

[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

> **重要**：如果 FileBrowser 可执行文件不在 `/usr/local/bin/filebrowser/` 路径，请修改 `ExecStart` 中的路径。
> 

### 重载 systemd 配置

```bash
sudo systemctl daemon-reload
```

### 启动并设置开机自启

```bash
sudo systemctl start filebrowser
sudo systemctl enable filebrowser
```

### 常用管理命令

```bash
# 启动服务
sudo systemctl start filebrowser

# 重启服务
sudo systemctl restart filebrowser

# 停止服务
sudo systemctl stop filebrowser

# 开机自启
sudo systemctl enable filebrowser

# 取消开机自启
sudo systemctl disable filebrowser

# 查看运行状态
sudo systemctl status filebrowser
```

---

## 后续配置

### 用户管理

登录 FileBrowser 后，可以在**设置 → 用户管理**中：

- 修改用户名和密码
- 修改目录访问范围（默认为根目录）
- 配置用户权限

![](https://oooutman.github.io/img/post/FileBrowser/filebrowser_02.jpg)

![](https://oooutman.github.io/img/post/FileBrowser/filebrowser_03.jpg)

![](https://oooutman.github.io/img/post/FileBrowser/filebrowser_04.jpg)

### 启用 HTTPS（可选）

如果你的服务器已绑定域名并申请了 SSL 证书，可以启用 HTTPS 访问。

**操作步骤：**

1. 停止 FileBrowser 服务：

```bash
sudo systemctl stop filebrowser
```

1. 配置证书路径（修改为你的证书和私钥路径）：

```bash
filebrowser -d /data/filebrowser.db config set --cert /path/to/cert.pem --key /path/to/key.pem
```

1. 重新启动服务：

```bash
sudo systemctl start filebrowser
```

现在就可以通过 [`https://域名:端口`](https://域名:端口) 访问了。

---

✅ **配置完成！**
