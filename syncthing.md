## 1. 下载安装部署

### 下载安装包

```bash
sudo wget -P /tmp https://github.com/syncthing/syncthing/releases/download/v1.29.2/syncthing-linux-arm64-v1.29.2.tar.gz
```

### 解压安装包

进入 `/tmp` 目录并解压：

```bash
cd /tmp && sudo tar -zxvf syncthing-linux-arm64-v1.29.2.tar.gz
```

### 移动到安装目录

```bash
mv syncthing-linux-arm64-v1.29.2 /usr/local/bin/syncthing
```

### 启动 Syncthing

进入安装目录：

```bash
cd /usr/local/bin/syncthing
```

运行 Syncthing：

```bash
./syncthing serve
```

![IMG_3814.png](attachment:db6b9d57-e629-4256-a7fc-fceb7b6ede49:IMG_3814.png)

如上图所示，服务成功运行。按 `Ctrl+C` 可停止服务。

### 允许远程访问

使用以下命令配置允许所有 IP 访问：

```bash
./syncthing serve --gui-address=0.0.0.0:8384
```

> **注意**：需要在服务器防火墙中放行 `8384` 端口。
> 

---

## 2. 配置同步文件夹

### 查看设备 ID

启动服务后，可以通过 Web 界面查看设备 ID：

![IMG_3815.png](attachment:98bd88c3-ce5b-4d97-a9cf-ba4b4118ccf1:IMG_3815.png)

![IMG_3816.png](attachment:f9f6d877-5354-445d-b3fe-1acb9ab41c63:IMG_3816.png)

### 添加远程设备

复制服务端的设备 ID 后，打开客户端的 Web 管理界面进行配置：

![IMG_3817.png](attachment:2da839f9-a9cc-424f-a4ce-a5cb80f25d1d:IMG_3817.png)

![IMG_3818.png](attachment:491a65f7-7989-4923-ae1a-4e4ae275ce35:IMG_3818.png)

---

## 3. 开机自启配置

### Windows 系统

### 创建启动脚本

在 Syncthing 根目录中新建 `start.bat` 文件：

![IMG_3819.png](attachment:f2f056b4-a92b-437a-8c9b-fd4a329c92b8:IMG_3819.png)

### 脚本内容

```
@echo off
 
if "%1"=="h" goto begin
 
start mshta vbscript:createobject("[wscript.shell](http://wscript.shell)").run("""%~nx0"" h",0)(window.close)&&exit
 
:begin
 
cd /d C:\Program Files\syncthing && syncthing.exe serve --no-browser --no-restart --logflags=0
```

> **重要**：将 `C:\Program Files\syncthing` 修改为你的实际安装路径。
> 

![IMG_3820.png](attachment:96406869-cd55-4c18-ac56-c83a03ddafd7:IMG_3820.png)

### 添加到开机启动

1. 右键 bat 文件，创建快捷方式：

![IMG_3821.png](attachment:2653420e-bc38-41fe-aeae-5b80c7b102ec:IMG_3821.png)

1. 按 `Win + R` 打开运行对话框，输入 `shell:startup` 并回车
2. 将快捷方式移动到打开的启动文件夹中：

![IMG_3822.png](attachment:8060b9f6-4a09-4e7c-adc3-b2e822d261e0:IMG_3822.png)

### Linux 系统

### 创建 systemd 服务

创建服务配置文件（`@` 后填写用户名）：

```bash
sudo vim /etc/systemd/system/syncthing@ubuntu.service
```

### 服务配置内容

```
[Unit]
Description=Syncthing - Open Source Continuous File Synchronization for %I
Documentation=man:syncthing(1)
After=[network.target](http://network.target)
StartLimitIntervalSec=60
StartLimitBurst=4
 
[Service]
User=%i
ExecStart=/usr/bin/syncthing/syncthing serve --no-browser --no-restart --logflags=0 --gui-address=0.0.0.0:8384
Restart=on-failure
RestartSec=1
SuccessExitStatus=3 4
RestartForceExitStatus=3 4
 
# Hardening
ProtectSystem=full
PrivateTmp=true
SystemCallArchitectures=native
MemoryDenyWriteExecute=true
NoNewPrivileges=true
 
# Elevated permissions to sync ownership (disabled by default),
# see https://docs.syncthing.net/advanced/folder-sync-ownership
#AmbientCapabilities=CAP_CHOWN CAP_FOWNER
 
[Install]
WantedBy=[multi-user.target](http://multi-user.target)
```

### 设置权限并启动

提升文件权限并重载配置：

```bash
sudo chmod +x /etc/systemd/system/syncthing@ubuntu.service && sudo systemctl daemon-reload
```

启动服务并设置开机自启：

```bash
sudo systemctl enable --now syncthing@ubuntu
```

### 常用管理命令

重启服务：

```bash
sudo systemctl restart syncthing@ubuntu
```

查看运行状态：

```bash
sudo systemctl status syncthing@ubuntu
```
