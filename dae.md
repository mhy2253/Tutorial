## 配置文件

- 1.下载最新发型版(amd64),解压安装包
```
wget https://github.com/daeuniverse/dae/releases/download/0.8.0/dae-linux-x86_64.zip && unzip dae-linux-x86_64.zip
```
- 2.创建配置目录和配置文件
```
mkdir -p /etc/dae && vim /etc/dae.config.dae
```
- 3.设置配置文件权限,限制只有配置文件所有者才可以读写。不设置dae无法启动
```
chmod 0640 /etc/dae/config.dae
```
## Geo数据库文件
- 1. 创建数据目录
```
mkdir -p /usr/local/share/dae/
```

- 2.将geo数据库文件移动到数据目录
```
mv geoip.dat geosite.dat /usr/local/share/dae/
```
## 主程序文件
- 1.添加可执行权限 *文件名不同架构不一样
```
chmod +x dae-linux-x86_64
```
- 2.将主程序文件安装到用户bin目录
```
mv dae-linux-x86_64 /usr/bin/dae
```
## 服务文件
- 1.将服务文件移动到systemd目录
```
mv dae.service /etc/systemd/system/
```
- 2.刷新配置
```
sudo systemctl daemon-reload
```

## 服务管理

- 1.启动+自启
```
systemctl enable dae.service --now
```
- 2.状态
```
systemctl status dae.service
```
- 3.重启
```
systemctl restart dae.service
```
- 4.重载配置
```
systemctl reload dae.service
```
- 5.日志
```
journalctl -xfu dae.service
```














