# 安装 C 环境

工物科协为大家推荐以下搭建 C 开发环境的方法, 并且会帮助解决配置 DevC++, msys2 以及远程服务器 C 环境过程中出现的问题.

限于人力不足, 其他搭建 C 环境的方法不一定得到 debug 支持.

## 安装 DevC++ 集成开发环境

> **注意**: DevC++ 安装方便, 然而开发体验一般.

### 安装与设置

文件名为 `Dev-Cpp 5.11 TDM-GCC 4.9.2 Setup.exe`, 访问 [sourceforge](https://sourceforge.net/projects/orwelldevcpp/) 上点击 Download 即可下载, 或者可以通过科协文件服务器提供的[镜像](https://cfile.thudep.com:7100/mirror/)下载.

下载后双击下载文件, 依次点击 `OK`, `I agree`, `Next`, `Install` 即可安装.

安装后点击 `Finish` (一般会自动打开 Dev-c++, 没有的话自己打开桌面的快捷方式), 随后选择 `简体中文`, 点击 `Next`, `Next`, `OK` 即可完成设置.

### 使用

在打开的程序左上角点击 `文件`, `新建`, `源代码`.

将以下代码复制到占较大面积的白色输入框中:

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

按键盘上的 <F9>, 选择一个文件夹保存源代码, 修改文件名后按下 `保存`, 再按下键盘上的 <F10> 即可运行.

## 安装 msys2 配置本地 C 环境并使用 VSCode 编辑器

## 配置远程服务器 C 环境并使用 VSCode 编辑器

科协将会为有需要的选手提供服务器账号, 服务器上已经预装了 clang 与 gcc.

选手可以在 VSCode 编辑器中通过 ssh 获得服务器开发环境.

> **注意**: 远程服务器 C 环境只在网络访问稳定的情况下可用.

## 安装 WSL Debian 发行版并使用 VSCode 编辑器

> **注意**: 使用 WSL 要求熟悉 Linux 操作.

## 安装 CLion 集成开发环境
