# uv 中文文档

> 一个用 Rust 编写的极速 Python 包和项目管理工具。

使用热缓存安装 Trio 的依赖项。

## 核心亮点

- 🚀 一个工具替代 pip、pip-tools、pipx、poetry、pyenv、twine、virtualenv 等
- ⚡️ 比 pip 快 10-100 倍
- 🗂️ 提供全面的项目管理功能，包含通用锁文件
- ❇️ 运行脚本，支持内联依赖元数据
- 🐍 安装和管理 Python 版本
- 🛠️ 运行和安装以 Python 包形式发布的工具
- 🔩 包含 pip 兼容接口，在熟悉 CLI 的同时获得性能提升
- 🏢 支持 Cargo 风格的工作区用于可扩展项目
- 💾 磁盘空间高效，通过全局缓存实现依赖去重
- ⏬ 无需 Rust 或 Python 即可通过 curl 或 pip 安装
- 🖥️ 支持 macOS、Linux 和 Windows

uv 由 Astral 提供支持，他们是 Ruff 的创造者。

## 安装

使用我们的官方独立安装程序安装 uv：

```bash
# macOS / Linux
$ curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows
$ powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装完成后，查看入门指南或继续阅读简要概述。

> **提示**：uv 也可以通过 pip、Homebrew 等方式安装。所有安装方法请参阅安装页面。

## 项目管理

uv 管理项目依赖和环境，支持锁文件、工作区等功能，类似于 rye 或 poetry：

```bash
$ uv init example
初始化项目 `example` 位于 `/home/user/example`

$ cd example

$ uv add ruff
创建虚拟环境于: .venv
在 170ms 内解析了 2 个包
构建 example @ file:///home/user/example
在 627ms 内准备了 2 个包
在 1ms 内安装了 2 个包
 + example==0.1.0 (from file:///home/user/example)
 + ruff==0.5.4

$ uv run ruff check
所有检查通过!

$ uv lock
在 0.33ms 内解析了 2 个包

$ uv sync
在 0.70ms 内解析了 2 个包
在 0.02ms 内审核了 1 个包
```

查看项目指南开始使用。

uv 还支持构建和发布项目，即使这些项目不是用 uv 管理的。了解更多请参阅打包指南。

## 脚本管理

uv 为单文件脚本管理依赖和环境。

创建新脚本并添加内联元数据声明其依赖：

```bash
$ echo 'import requests; print(requests.get("https://astral.sh"))' > example.py
$ uv add --script example.py requests
已更新 `example.py`
```

然后在隔离的虚拟环境中运行脚本：

```bash
$ uv run example.py
从 example.py 读取内联脚本元数据
在 12ms 内安装了 5 个包
<Response [200]>
```

查看脚本指南开始使用。

## 工具

uv 可以执行和安装 Python 包提供的命令行工具，类似于 pipx。

使用 `uvx`（`uv tool run` 的别名）在临时环境中运行工具：

```bash
$ uvx pycowsay 'hello world!'
Resolved 1 package in 167ms
Installed 1 package in 9ms
 + pycowsay==0.0.0.2
  """
  \ ^__^
   \ (oo)\_______
     (__)\ )\/\
       ||----w |
       ||     ||
```

使用 `uv tool install` 安装工具：

```bash
$ uv tool install ruff
Resolved 1 package in 6ms
Installed 1 package in 2ms
 + ruff==0.5.4
Installed 1 executable: ruff

$ ruff --version
ruff 0.5.4
```

参考工具指南开始使用。

## Python 版本

uv 可以安装 Python 并快速切换版本。

安装多个 Python 版本：

```bash
$ uv python install 3.10 3.11 3.12
Searching for Python versions matching: Python 3.10
Searching for Python versions matching: Python 3.11
Searching for Python versions matching: Python 3.12
Installed 3 versions in 3.42s
 + cpython-3.10.14-macos-aarch64-none
 + cpython-3.11.9-macos-aarch64-none
 + cpython-3.12.4-macos-aarch64-none
```

按需下载 Python 版本：

```bash
$ uv venv --python 3.12.0
Using CPython 3.12.0
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate

$ uv run --python pypy@3.8 -- python
Python 3.8.16 (a9dbdca6fc3286b0addd2240f11d97d8e8de187a, Dec 29 2022, 11:45:30)
[PyPy 7.3.11 with GCC Apple LLVM 13.1.6 (clang-1316.0.21.2.5)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>>
```

在当前目录使用特定 Python 版本：

```bash
$ uv python pin 3.11
Pinned `.python-version` to `3.11`
```

参考安装 Python 指南开始使用。

## pip 接口兼容

uv 提供了与常见 pip、pip-tools 和 virtualenv 命令完全兼容的替代方案。

uv 通过高级功能扩展了这些接口，包括依赖版本覆盖、跨平台解析、可复现的解析方案、替代解析策略等。

无需改变现有工作流即可迁移到 uv —— 通过 `uv pip` 接口体验 10-100 倍的速度提升。

将依赖编译为跨平台的需求文件：

```bash
$ uv pip compile docs/requirements.in \
   --universal \
   --output-file docs/requirements.txt
Resolved 43 packages in 12ms
```

创建虚拟环境：

```bash
$ uv venv
Using CPython 3.12.3
Creating virtual environment at: .venv
Activate with: source .venv/bin/activate
```

安装锁定的依赖项：

```bash
$ uv pip sync docs/requirements.txt
Resolved 43 packages in 11ms
Installed 43 packages in 208ms
 + babel==2.15.0
 + black==24.4.2
 + certifi==2024.7.4
 ...
```

查看 pip 接口文档开始使用。

## 了解更多

查看入门指南或直接跳转至使用指南开始使用 uv。

---

*2025年5月29日*
*Made with Material for MkDocs*
