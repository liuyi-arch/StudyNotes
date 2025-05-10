vscode中如何运行js文件？
---
### 1. 使用node.js运行
1. 下载node[2][3]；
2. 打开终端：Ctrl + ` ；
3. `node index.js`运行；

### 2. VSCode 插件 Code Runner 运行
应用商店下载Code Runner；

引用：
---

### [2]Homebrew介绍
Homebrew 是 macOS 和 Linux 上的一个 包管理器，它的作用类似于 Ubuntu 上的 apt 或 Python 中的 pip.

特点：
1. 只需一行命令即可安装软件，比如 brew install node；
2. 不会影响系统默认的软件路径，安装的软件大多位于 /opt/homebrew 或 /usr/local；

### [3]Homebrew安装
1. ~~终端[1]输入命令：`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)`，出现系统代理导致curl 无法正常工作错误；~~
2. ~~解决：取消http_proxy 或 https_proxy 设置`unset http_proxy` `unset https_proxy` ，重新执行Homebrew 安装命令，仍然出现
   `error: RPC failed; curl 18 HTTP/2 stream 5 was reset`错误，这是由于网络连接不稳定，可以采用国内镜像源；~~
3. 设置环境变量为清华镜像（临时）：`export HOMEBREW_BREW_GIT_REMOTE=https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git`
   `export HOMEBREW_CORE_GIT_REMOTE=https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git`；
4. 用清华镜像源安装Homebrew：`/bin/bash -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"`，之后按命令行提示配置环境变量、重启终端
   或者运行`source /Users/liuliu/.zprofile`即可使用brew；

### [1] mac中打开终端
1. Command (⌘) + Space 打开 Spotlight 搜索；
2. 输入 Terminal，回车；

注意：
---

- 安装pip/brew包需在系统终端执行，而不是比如vscode中任一路径下终端！

- `Ctrl + C`中断终端安装命令；

