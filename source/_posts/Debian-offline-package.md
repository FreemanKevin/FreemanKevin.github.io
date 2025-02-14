---
title: Debian 系统下制作离线安装包
date: 2024-12-20 12:17:25
tags:
    - Apt
    - Offline
    - Debian
category: Linux
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;离线安装在 Linux 系统中是一个常见需求，特别是在无法联网的环境中。本文介绍了如何在 Debian 系列操作系统（如 Debian 和 Ubuntu）上创建和使用离线安装包的完整步骤。首先，我们在联网环境下下载所需的软件包及其依赖，并创建本地 APT 仓库。然后，我们将这些包复制到离线系统上，并配置本地仓库以便进行安装。通过这些步骤，你可以方便地在离线环境中安装所需的软件。

<!-- more -->

## 在联网的环境下

### 下载软件包及其依赖

首先，确定你需要的软件包和版本。使用APT的下载功能来获取软件包和它的所有依赖，但不安装它们。

```bash
sudo apt-get install --download-only <package-name>
```

这会将软件包及其依赖下载到 `/var/cache/apt/archives/` 目录。

### 复制下载的包

将下载的 `.deb` 包复制到一个移动存储设备上。

```bash
cp /var/cache/apt/archives/*.deb /path/to/your/usb-drive/debian-offline-packages
```


### 创建本地APT仓库

在移动存储设备的相同目录下，使用 `dpkg-scanpackages` 工具来创建 `Packages.gz` 索引文件。如果没有安装 `dpkg-dev`，需要先安装它。

```bash
sudo apt-get install dpkg-dev
cd /path/to/your/usb-drive/debian-offline-packages
dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```

## 在离线的环境下

### 准备本地APT仓库

将移动存储设备连接到离线的Debian系统。复制 `.deb` 包到本地文件系统，并保留目录结构。

```bash
mkdir -p /path/to/local/repo
cp /path/to/your/usb-drive/debian-offline-packages/* /path/to/local/repo
```

### 添加本地仓库到APT源

创建一个APT源列表文件，以便APT可以使用本地仓库。

```bash
echo 'deb [trusted=yes] file:///path/to/local/repo ./' | sudo tee /etc/apt/sources.list.d/local-offline-repo.list
```

### 更新软件包列表

更新本地包数据库，使APT能够识别新的本地仓库。

```bash
sudo apt-get update
```

### 安装软件

现在，使用 `apt-get install` 命令安装软件包，APT将解决所有本地依赖。

```bash
sudo apt-get install <package-name>
```

## 注意事项

- 这个过程假定你已经有了移动存储设备，如USB驱动器，并且它已经被挂载到了你的系统上。
- `<package-name>` 是你想要下载并安装的软件包名称。
- `/path/to/your/usb-drive` 是你USB驱动器的挂载点。
- `/path/to/local/repo` 是你在离线机器上创建的本地仓库目录的路径。

按照这些步骤，你应该能够在Debian系列系统上进行离线软件安装。



