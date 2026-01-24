# 第零次培训: 安装 C 环境

> 查看宣讲会与第零次培训的[回放与文件](https://cfile.thudep.com:7100/11/talk0).

工物科协为大家提供以下 5 种搭建 C 开发环境的方案, 以下均针对 windows 用户, 如果您为 linux 或 macos 用户, 应该能够更为简单的搭建 C 开发环境.

## 方案1: 使用 DevC++ 集成开发环境

> **注意**: DevC++ 安装方便, 比较适合刚入门编程的同学, 但开发体验一般.

### 安装与设置

文件名为 `Dev-Cpp 5.11 TDM-GCC 4.9.2 Setup.exe`, 访问 [sourceforge](https://sourceforge.net/projects/orwelldevcpp/) 上点击 Download 即可下载, 或者可以通过科协文件服务器提供的[镜像](https://cfile.thudep.com:7100/mirror/Dev-Cpp%205.11%20TDM-GCC%204.9.2%20Setup.exe)下载.

下载后双击下载文件, 依次点击 `OK`, `I agree`, `Next`, (选择安装位置) `Install` 即可完成安装.

安装后点击 `Finish` (一般会自动打开 Dev-c++, 没有的话自己打开桌面的快捷方式), 随后选择 `简体中文`, 点击 `Next`, `Next`, `OK` 即可完成设置.

### 使用测试

在打开的程序左上角点击 `文件`, `新建`, `源代码`.

将以下示例代码复制到占较大面积的白色输入框中:

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

按键盘上的 `F9`, 选择一个文件夹保存源代码, 修改文件名后按下 `保存`, 再按下键盘上的 `F10` 即可编译运行.

## 方案2: 使用 Visual Studio 集成开发环境

> **注意**: Visual Studio 操作简单易上手, 功能齐全, 但体积较大.

可参考[VS安装](https://cfile.thudep.com:7100/11/evironment/VS%E5%AE%89%E8%A3%85.mp4)教程视频, 视频中的安装链接为 [https://visualstudio.microsoft.com/](https://visualstudio.microsoft.com/
) .

## 方案3-5: 使用 VSCode 编辑器

> **注意**: Visual Studio Code (编辑器) 与 Visual Studio (集成开发环境) 是两个完全不同的软件.

### 方案3: 使用 MinGW 作为 windows 上的编译器

可参考[VSCode安装](https://cfile.thudep.com:7100/11/evironment/VSCode%E5%AE%89%E8%A3%85.mp4)教程视频, 视频中的安装链接为 [https://code.visualstudio.com/](https://code.visualstudio.com/) , 视频中的 MinGW-W64 的 github 下载链接为 [https://github.com/niXman/mingw-builds-binaries/releases/](https://github.com/niXman/mingw-builds-binaries/releases/) , 清华云盘中的文件已转移到我们的[赛事文件](https://cfile.thudep.com:7100/mirror/MinGW-W64/)中.

### 方案4: 安装 WSL Debian 发行版在 linux 环境下开发

> **注意**: 使用 WSL 要求了解 Linux 操作.

有关 WSL2 的安装, 可以参考 [WSL2 安装教程](https://physics-data.meow.plus/faq/env/windows/); 有关 linux 基础, 可以参考 [Linux 基础教程](https://hep.tsinghua.edu.cn/~orv/teaching/physics-data/IT-2023-6-Linux-Tutorial.pdf).

简单的来说, 你可以在 windows 终端 (cmd 或 PowerShell) 中输入以下命令安装 WSL2 :
```cmd
wsl --install -d Debian
```
安装完成后, 需要设置用户名与密码 (注意设置密码时, 输入的密码不会出现在屏幕上), 然后即可进入 WSL. 可以使用终端进行开发, 也可以使用 VSCode 的 WSL 插件来连接到 WSL.

可以使用以下命令来安装 `gcc` 等编译工具:
```bash
sudo apt update
sudo apt install build-essential
```
对于 `gcc` 相关命令, 可以参考[菜鸟教程](https://www.runoob.com/w3cnote/gcc-parameter-detail.html). 在初学阶段, 只需要知道可以用以下命令把源代码编译为可执行文件 (可以加 `-O2` 等优化参数)：
```bash
gcc main.c -o main
```
然后使用如下命令运行:
```bash
./main
```

### 方案5: 配置远程服务器并使用 ssh 连接

> **注意**: 远程服务器只在网络访问稳定的情况下可用, 且要求了解 Linux 操作.

科协将会为有需要的选手提供服务器账号, 服务器上已经预装了 `gcc`, 请有需要的选手与比赛负责人私信联系, 负责人将会为您在服务器上创建临时账号 (赛后将收回) 并指导连接. 

在远程服务器中可以使用终端进行开发, 也可以使用 VSCode 的 Remote-SSH 插件连接, 获得与本地开发相接近的体验.
