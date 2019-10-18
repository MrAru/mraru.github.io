---
layout:     post
title:      Manjaro For Windows WSL2
subtitle:   美观是第一生产力
date:       2019-10-17
author:     Mr.Aru
header-img: https://i.loli.net/2019/10/18/4GsciF31JTNUjKv.jpg
catalog: true
tags:
    - Linux
    - Code
---

### 前言

在 Windows 上进行开发一直是一个令人头疼的问题。难看难用的非 Unix 终端、缺乏好用的包管理系统、繁琐的环境变量配置，均让其在开发体验上难以匹敌 Linux。

**W**indows **S**ubsystem for **L**inux 的出现极大解决了这些问题。通过 WSL，我们可以直接在 Windows 上拥有与 Linux 完全一致的开发体验，同时不必舍弃 Windows 本身的强大生产力。

人生苦短，我选 WSL。

### 配置

0. 准备工作

    - Windows 10 至少为 Windows 10 Fall Creators Update 或更高。
    - Windows 用户名不能有空格。
    - Windows 用户名不能为中文。

1. 开启 WSL 特性选项

    以管理员身份打开 PowerShell，输入并运行

    ```powershell
    Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
    ```
    ```powershell
    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
    ```

    完成后重启系统。

2. 下载发行版并安装

    下文以 Manjaro 为例，Manjaro 也是本文推荐的发行版。

    下载[官方镜像文件](https://manjaro.org/download/)或[第三方打包的镜像文件](https://github.com/riag/manjaro-linux-for-wsl#%E4%B8%8B%E8%BD%BD)，下载[LxRunOffline](https://github.com/DDoSolitary/LxRunOffline/releases)。

    ```powershell
    .\LxRunOffline.exe install -n Manjaro -f <filepath to mirror file> -d <folderpath to setup> -r root.x86_64
    ```

    安装完成后输入

    ```powershell
    bash ~
    ```

    或

    ```powershell
    wsl ~
    ```

    启动。不加 `~` 会使 WSL 从当前路径打开，`~` 等价于 `cd ~`。

3. Manjaro 设置

    以下命令均需要 `root` 权限。

    ```bash
    # 生成并排序中国镜像站列表
    # 推荐选择 TUNA(清华) / USTC(科大)
    pacman-mirrors -i -c China -m rank
    
    # 强制刷新软件源数据库并更新
    pacman -Syyu

    # 安装 oh-my-zsh
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

    # 安装 C++ 基础编译环境
    pacman -S base-devel --needed
    ```

    由于 DrvFs 文件系统的权限问题，导致 Windows 原有 NTFS 文件系统中的文件在 WSL 下权限全部变成 `0777`，即在执行 `ls` 或 `cd` 命令后，显示结果的背景色会出现问题。解决方法之一为在 `.zshrc` 文件末添加

    ```
    # Change ls colours
    LS_COLORS="ow=01;36;40" && export LS_COLORS
    
    # make cd use the ls colours
    zstyle ':completion:*' list-colors "${(@s.:.)LS_COLORS}"
    autoload -Uz compinit
    compinit
    ```

    并加载设置

    ```
    source ~/.zshrc
    ```

4. VS Code 设置

    在 Windows 侧打开 VS Code。如果前述操作无误，VS Code 会自动提醒安装 `Remote - WSL` 插件。安装完成后选择 Manjaro 并连接，VS Code 及 `Remote - WSL` 插件将自动安装在远程机上。

    Windows 盘符挂载于 WSL 侧 `/mnt` 文件夹，WSL 挂载于 Windows 侧 `<folderpath to setup>/rootfs` 文件夹。

至此，绝大多数通用 WSL 配置已完成，你应该可以直接上手开发了。最后祝你：开发愉快，再见。

---

参考

> [Dev on Windows with WSL](https://dowww.spencerwoo.com/)

> [Manjaro Linux for WSL](https://github.com/riag/manjaro-linux-for-wsl)

> [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline)

---