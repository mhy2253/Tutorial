# Mac Python 环境重置指南

---

## ① 先把刚才乱装的东西清干净（用户目录）

**删除用户目录里的 Python 3.9 包**（包括刚才升级的 pip）：

```bash
rm -rf ~/Library/Python/3.9
```

如果你以前还装过别的版本（比如 3.8），也可以顺手看看：

```bash
ls ~/Library/Python
```

如果看到有别的版本，比如 3.8、3.11，想一起清理就：

```bash
rm -rf ~/Library/Python/3.8
rm -rf ~/Library/Python/3.11
```

> ℹ️ 这些都是你用户级安装的包，删掉不会影响系统，也不会影响 Homebrew。

---

## ② 确认/安装 Homebrew（如果已经有可以略读）

先看看你有没有 `brew`：

```bash
brew --version
```

- 如果有版本号，说明装过，**直接跳到 ③**
- 如果提示 `command not found`，就安装一下 Homebrew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装结束后，终端一般会提示你把一行 `eval "$(/opt/homebrew/bin/brew shellenv)"` 之类的东西加到 `~/.zprofile` 或 `~/.zshrc`，按它提示做。

> 💡 如果你用的是 Apple Silicon Mac，Homebrew 大概率在 `/opt/homebrew`；Intel 在 `/usr/local/Homebrew`

---

## ③ 用 Homebrew 安装"主力 Python"

```bash
brew install python
```

装完后，检查一下你用的是不是 Homebrew 的 `python3`：

```bash
which python3
which pip3
python3 --version
pip3 --version
```

**理想输出应该类似：**

| 命令 | 期望输出 |
|------|----------|
| `which python3` | `/opt/homebrew/bin/python3` 或 `/usr/local/bin/python3` |
| `pip3 --version` | 路径指向 `.../Cellar/python/...` 或 `.../Frameworks/...`，不要再看到 `CommandLineTools` |

### 如果还是指向旧路径

如果还是指向 `/Library/Developer/CommandLineTools/...`，说明 PATH 顺序有问题，可以在 `~/.zshrc` 里加一句，把 Homebrew 放前面：

```bash
export PATH="/opt/homebrew/bin:/opt/homebrew/sbin:$PATH"   # Apple Silicon
```

然后让配置生效：

```bash
source ~/.zshrc
```

再确认一次：

```bash
which python3
which pip3
```

确认已经变成 Homebrew 版本。

---

## ④ 建一个"干净的主环境（虚拟环境）"

推荐所有项目都用 venv，别在"全局环境"里乱装包。

**1. 建一个专门放虚拟环境的目录**（可选但推荐）：

```bash
mkdir -p ~/venvs
```

**2. 创建一个通用环境**，比如叫 `base`：

```bash
python3 -m venv ~/venvs/base
```

**3. 激活它：**

```bash
source ~/venvs/base/bin/activate
```

终端前面一般会多一个 `(base)` 的前缀，表示当前 venv 已激活。

**4. 在这个环境里升级 pip：**

```bash
pip install --upgrade pip
```

**5. 装你常用的包**（示例）：

```bash
pip install numpy pandas matplotlib jupyter
```

以后只要想用这个环境：

```bash
source ~/venvs/base/bin/activate

# 用完了退出
deactivate
```

---

## ⑤ VSCode / 其他工具里用这个 Python

如果你用 VSCode 编码：

1. 打开 VSCode，安装 **Python 扩展**（Microsoft 出的那个）
2. 按 `Command+Shift+P` → 输入 `Python: Select Interpreter`
3. 选路径类似：`~/venvs/base/bin/python`

之后这个工作区就会默认用这个 venv 运行、调试。

---

## ⑥ 快速自检：确认一切正常

在激活了 venv 的情况下（前面有 `(base)`），执行：

```bash
python -c "import sys, platform; print(sys.executable); print(sys.version); print(platform.platform())"
```

```bash
python -c "import numpy, pandas; print('numpy:', numpy.__version__, 'pandas:', pandas.__version__)"
```

你应该看到：

- `sys.executable` 在 `~/venvs/base/bin/python`
- Python 版本是 Homebrew 装的 3.12/3.13 之类
- numpy / pandas 正常打印版本号，无报错

---

## ⑦ 以后记住几条"铁律"

> ⚠️ **1. 只用 Homebrew 的 `python3` / `pip3`**
> 不再用 `/Library/Developer/CommandLineTools/...` 那个。
>
> **2. 装新包之前，优先激活 venv：**
>
> ```bash
> source ~/venvs/base/bin/activate
> pip install xxx
> ```
>
> **3. 遇到奇怪报错，先看看：**
>
> ```bash
> which python3
> which pip3
> ```

---

如果你愿意，可以把下面这些命令输出贴给我看（激活 `base` 后执行）：

```bash
which python3
python3 --version
which pip
pip --version
python -m site
```