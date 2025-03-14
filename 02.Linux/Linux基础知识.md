# Linux 基础知识

## 1. 操作系统基础

### 1.1 操作系统的定义
- **定义**：操作系统（Operating System，简称OS）是软件的一部分，它是硬件基础上的第一层软件，是硬件和其他软件沟通的桥梁。它控制其他程序运行，管理系统资源，并提供基本的计算功能。

### 1.2 Linux 系统内核与发行套件的区别
- **Linux 系统内核**：由 Linus Torvalds 负责维护，提供硬件抽象层、硬盘及文件系统控制以及多任务功能的系统核心程序。
- **Linux 发行套件**：我们常说的 Linux 操作系统，是由 Linux 内核与各种常用软件的集合产品。

### 1.3 Linux 的特点
- 稳定且高效。
- 免费（或少许费用）。
- 漏洞少且快速修补。
- 多任务多用户。
- 更安全的用户与文件权限策略。
- 适合嵌入式系统。
- 相对不耗资源。

### 1.4 Linux 系统种类
- **红帽企业版 Linux (RHEL)**：使用最广泛的 Linux 系统，性能与稳定性强，收费系统。
- **Fedora**：红帽公司发布的桌面版系统，免费体验新技术，成熟后加入 RHEL。
- **CentOS**：基于 RHEL 重新编译的免费系统，广泛使用。
- **Deepin**：中国发行，集成优秀开源软件。
- **Debian**：稳定性、安全性强，提供免费基础支持。
- **Ubuntu**：基于 Debian，对新款硬件兼容性强，适合桌面和服务器。

## 2. 终端与 Shell

### 2.1 终端连接服务器
- **命令**：`ssh 用户名@服务器IP`
- **范例**：
  ```bash
  ssh root@121.42.11.34
  ```
- **说明**：通过 SSH 协议连接远程服务器，输入密码后即可登录。

### 2.2 Shell 的定义
- **定义**：Shell 是用户与内核交互的接口，提供命令行环境，支持变量、条件判断、循环操作等语法。

### 2.3 常见 Shell 类型
- Bourne Shell（sh）
- Bourne Again Shell（bash）
- C Shell（csh）
- TENEX C Shell（tcsh）
- Korn Shell（ksh）
- Z Shell（zsh）
- Friendly Interactive Shell（fish）

### 2.4 查看当前 Shell
- **命令**：`echo $SHELL`
- **范例**：
  ```bash
  echo $SHELL
  ```
- **说明**：显示当前使用的 Shell 类型。

## 3. 文件系统与目录操作

### 3.1 文件系统结构
- **说明**：Linux 文件系统采用分层的目录结构，所有文件和目录从根目录 `/` 开始。
- **范例**：
  ```bash
  /               # 根目录
  /bin            # 存放用户常用命令
  /etc            # 系统配置文件目录
  /home           # 用户主目录
  /usr            # 系统资源目录
  /var            # 可变数据目录
  ```

### 3.2 查看目录内容
- **命令**：`ls`
- **范例**：
  ```bash
  ls               # 列出当前目录下的文件和目录
  ls -l            # 列出详细信息
  ls -a            # 列出所有文件，包括隐藏文件
  ls -lh           # 以易读的格式显示文件大小
  ```

### 3.3 创建目录
- **命令**：`mkdir`
- **范例**：
  ```bash
  mkdir mydir      # 创建一个名为 mydir 的目录
  mkdir -p mydir/subdir  # 创建多级目录
  ```

### 3.4 删除目录
- **命令**：`rmdir`
- **范例**：
  ```bash
  rmdir mydir      # 删除一个空目录
  rm -r mydir      # 强制删除非空目录及其内容（谨慎使用）
  ```

### 3.5 切换目录
- **命令**：`cd`
- **范例**：
  ```bash
  cd mydir         # 切换到 mydir 目录
  cd ..            # 返回上一级目录
  cd               # 返回用户主目录
  ```

## 4. 文件操作

### 4.1 创建文件
- **命令**：`touch`
- **范例**：
  ```bash
  touch myfile.txt  # 创建一个空文件
  ```

### 4.2 查看文件内容
- **命令**：`cat`、`less`、`more`
- **范例**：
  ```bash
  cat myfile.txt   # 显示文件内容
  less myfile.txt  # 分页显示文件内容
  more myfile.txt  # 分页显示文件内容（与 less 类似）
  ```

### 4.3 编辑文件
- **命令**：`nano`、`vim`
- **范例**：
  ```bash
  nano myfile.txt  # 使用 nano 编辑器打开文件
  vim myfile.txt   # 使用 vim 编辑器打开文件
  ```

### 4.4 复制文件
- **命令**：`cp`
- **范例**：
  ```bash
  cp myfile.txt mycopy.txt  # 复制文件
  cp -r mydir mydir_backup  # 复制目录（递归复制）
  ```

### 4.5 移动/重命名文件
- **命令**：`mv`
- **范例**：
  ```bash
  mv myfile.txt mynewfile.txt  # 重命名文件
  mv myfile.txt /home/user/    # 移动文件到指定目录
  ```

### 4.6 删除文件
- **命令**：`rm`
- **范例**：
  ```bash
  rm myfile.txt  # 删除文件
  rm -f myfile.txt  # 强制删除文件（不提示确认）
  ```

## 5. 权限管理

### 5.1 查看文件权限
- **命令**：`ls -l`
- **范例**：
  ```bash
  ls -l myfile.txt  # 查看文件权限
  ```

### 5.2 修改文件权限
- **命令**：`chmod`
- **范例**：
  ```bash
  chmod 755 myfile.txt  # 设置文件权限为 rwxr-xr-x
  chmod u+x myfile.txt  # 为用户添加执行权限
  chmod g-w myfile.txt  # 移除组的写权限
  ```

### 5.3 修改文件所有者
- **命令**：`chown`
- **范例**：
  ```bash
  chown user myfile.txt  # 将文件所有者改为 user
  chown user:group myfile.txt  # 同时修改所有者和所属组
  ```

### 5.4 修改文件所属组
- **命令**：`chgrp`
- **范例**：
  ```bash
  chgrp group myfile.txt  # 将文件所属组改为 group
  ```

## 6. 系统管理

### 6.1 查看系统信息
- **命令**：`uname`、`df`、`free`
- **范例**：
  ```bash
  uname -a          # 显示系统信息
  df -h             # 查看磁盘使用情况
  free -h           # 查看内存使用情况
  ```

### 6.2 查看进程
- **命令**：`ps`、`top`、`htop`
- **范例**：
  ```bash
  ps aux            # 查看所有进程
  top               # 实时显示系统进程信息
  htop              # 更友好的进程查看工具（需要安装）
  ```

### 6.3 杀死进程
- **命令**：`kill`、`pkill`
- **范例**：
  ```bash
  kill 1234         # 杀死进程ID为1234的进程
  pkill processname  # 杀死名为 processname 的所有进程
  ```

### 6.4 查看网络连接
- **命令**：`netstat`、`ss`
- **范例**：
  ```bash
  netstat -tuln      # 查看所有监听的网络端口
  ss -tuln           # 类似于 netstat 的功能
  ```

### 6.5 查看日志文件
- **命令**：`cat`、`less`、`tail`
- **范例**：
  ```bash
  cat /var/log/messages  # 查看系统日志
  less /var/log/messages  # 分页查看系统日志
  tail -f /var/log/messages  # 实时查看日志文件的更新
  ```

## 7. 用户与组管理

### 7.1 添加用户
- **命令**：`useradd`
- **范例**：
  ```bash
  useradd newuser  # 添加一个新用户
  useradd -m newuser  # 添加新用户并创建主目录
  ```

### 7.2 删除用户
- **命令**：`userdel`
- **范例**：
  ```bash
  userdel newuser  # 删除用户
  userdel -r newuser  # 删除用户并删除其主目录
  ```

### 7.3 添加组
- **命令**：`groupadd`
- **范例**：
  ```bash
  groupadd newgroup  # 添加一个新组
  ```

### 7.4 删除组
- **命令**：`groupdel`
- **范例**：
  ```bash
  groupdel newgroup  # 删除组
  ```

### 7.5 修改用户密码
- **命令**：`passwd`
- **范例**：
  ```bash
  passwd newuser  # 修改用户密码
  ```

## 8. 软件包管理

### 8.1 安装软件包
- **命令**：`apt`（Debian/Ubuntu）、`yum`（CentOS）、`dnf`（Fedora）
- **范例**：
  ```bash
  sudo apt install nano  # Debian/Ubuntu 系统安装软件
  sudo yum install nano  # CentOS 系统安装软件
  sudo dnf install nano  # Fedora 系统安装软件
  ```

### 8.2 更新软件包
- **命令**：`apt`、`yum`、`dnf`
- **范例**：
  ```bash
  sudo apt update && sudo apt upgrade  # 更新 Debian/Ubuntu 系统软件
  sudo yum update  # 更新 CentOS 系统软件
  sudo dnf upgrade  # 更新 Fedora 系统软件
  ```

### 8.3 删除软件包
- **命令**：`apt`、`yum`、`dnf`
- **范例**：
  ```bash
  sudo apt remove nano  # 删除 Debian/Ubuntu 系统中的软件
  sudo yum remove nano  # 删除 CentOS 系统中的软件
  sudo dnf remove nano  # 删除 Fedora 系统中的软件
  ```

## 9. 网络配置

### 9.1 配置网络接口
- **文件**：`/etc/network/interfaces`（Debian/Ubuntu）、`/etc/netplan/*.yaml`（新版本 Ubuntu）、`/etc/sysconfig/network-scripts/ifcfg-*`（CentOS）
- **范例**：
  ```bash
  sudo nano /etc/network/interfaces
  sudo nano /etc/netplan/01-netcfg.yaml
  sudo nano /etc/sysconfig/network-scripts/ifcfg-eth0
  ```

### 9.2 配置 DNS
- **文件**：`/etc/resolv.conf`
- **范例**：
  ```bash
  sudo nano /etc/resolv.conf
  nameserver 8.8.8.8
  nameserver 8.8.4.4
  ```

### 9.3 查看网络配置
- **命令**：`ifconfig`、`ip`
- **范例**：
  ```bash
  ifconfig          # 查看网络接口配置
  ip addr show      # 查看网络接口配置（推荐）
  ```
