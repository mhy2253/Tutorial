# Mihomo 安装与配置指南

---

## 第一步：下载内核文件

1. 打开浏览器访问：[https://github.com/MetaCubeX/mihomo/releases](https://github.com/MetaCubeX/mihomo/releases)，下载带 `compatible` 字样的版本

2. 找到最新版本（**Latest**），下载以下文件：

   | 用户类型 | 文件名 |
   |----------|--------|
   | 普通用户 | `mihomo-windows-amd64-v版本号.zip` |
   | 老电脑用户 | `mihomo-windows-386-v版本号.zip` |

3. 解压 zip 文件，得到 `mihomo-windows-amd64.exe`

4. 新建一个文件夹，例如 `C:\mihomo\`，把 exe 文件放进去，并重命名为 `mihomo.exe`

---

## 第二步：准备配置文件

在 `C:\mihomo\` 文件夹内新建文件 `config.yaml`

---

## 第三步：运行 Mihomo

1. 按 `Win + R`，输入 `powershell`，回车打开 PowerShell

2. 输入以下命令进入 mihomo 目录：

   ```powershell
   cd C:\mihomo
   ```

3. 运行内核：

   ```powershell
   .\mihomo.exe -d .
   ```

4. 看到类似以下输出说明**运行成功**：

   ```
   INFO[0000] Start initial compatible provider default
   INFO[0000] mihomo v1.x.x started
   ```

> ⚠️ 如果弹出防火墙提示，点击**允许访问**

---

## 第四步：设置系统代理

1. 打开 **设置 → 网络和 Internet → 代理**
2. 关闭"自动检测设置"
3. 打开"使用代理服务器"，填写：

   | 字段 | 值 |
   |------|----|
   | 地址 | `127.0.0.1` |
   | 端口 | `7890` |

4. 点击**保存**

---

## 使用 VBScript 隐藏窗口运行 Mihomo

### 原理

VBScript 可以在完全不显示任何窗口的情况下启动程序，关闭终端后进程依然在后台运行。

---

### 第一步：创建 VBScript 启动脚本

在 `C:\mihomo\` 文件夹内新建文件 `start.vbs`，内容如下：

```vbscript
Set ws = CreateObject("WScript.Shell")
ws.Run "C:\mihomo\mihomo.exe -d C:\mihomo", 0, False
```

> - `0` 表示隐藏窗口
> - `False` 表示不等待程序结束，直接返回
> - 路径如果不是 `C:\mihomo` 请自行修改

---

### 第二步：运行脚本

双击 `start.vbs` 即可，没有任何窗口弹出，Mihomo 已在后台运行。

**验证是否在运行：**
打开任务管理器（`Ctrl + Shift + Esc`）→ **详细信息**，找到 `mihomo.exe` 说明运行成功。

---

### 第三步：如何停止 Mihomo

- **方法一：** 任务管理器 → 找到 `mihomo.exe` → 右键 → **结束任务**

- **方法二：** 用 PowerShell 一条命令结束：

  ```powershell
  taskkill /IM mihomo.exe /F
  ```

---

## 可选：开机自动启动

如果希望开机自动运行，把 `start.vbs` 的**快捷方式**放入启动文件夹：

1. 按 `Win + R`，输入：

   ```
   shell:startup
   ```

   回车后会打开启动文件夹

2. 把 `start.vbs` 的快捷方式复制进去即可

这样每次开机就会自动在后台启动 Mihomo，无需手动操作。
