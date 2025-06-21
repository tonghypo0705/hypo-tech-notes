# 使用 VMware Workstation Pro 搭建 NAT 网络模式的虚拟机集群环境

## 一、前言

在学习或工作中，我们常常需要搭建如 Zookeeper、Redis、Hadoop 等分布式集群。但现实中很难拥有多台实体服务器，因此，利用 VMware 虚拟机构建集群环境是一种性价比高的解决方案。

本文将详尽介绍如何在 Windows 系统上使用 VMware Workstation Pro 通过 NAT 网络模式搭建 CentOS 虚拟机环境，为后续部署分布式系统做准备。

---

## 二、安装准备

### 1. 硬件要求

- 一台性能良好的 Windows 主机（建议 16G 内存以上，开启 VT 虚拟化支持）
- 足够的硬盘空间（建议每台虚拟机不少于 20G）

### 2. 软件准备

- VMware Workstation Pro（安装包及秘钥）
- CentOS 镜像文件（推荐 CentOS 7）

> VMware 激活秘钥示例：`JU090-6039P-08409-8J0QH-2YR7F`  
> CentOS 镜像下载：  
> 链接：https://pan.baidu.com/s/1P8eA3ozDsQYjVS4r8Z_6ZA  
> 提取码：`pjtd`

---

## 三、CentOS 虚拟机环境搭建

### 1. 安装 VMware 并创建虚拟机

- 安装 VMware Workstation Pro
- 创建新的虚拟机，推荐使用“自定义（高级）”选项
- 分配合适的内存（建议 2G 以上）与 CPU 数量
- 挂载 CentOS ISO 镜像并启动安装流程

### 2. 配置网络模式为 NAT

- 虚拟机设置 → 网络适配器 → 选择 “NAT: 使用宿主网络共享访问”  
- 保存设置并启动虚拟机

<img src="assets/%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83/image-20250621114051466.png" style="zoom:25%;" />

<img src="assets/%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83/image-20250621114135054.png" alt="image-20250621114135054" style="zoom:25%;" />

- 初始化centos系统

<img src="assets/%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83/image-20250621141945771.png" alt="image-20250621141945771" style="zoom:25%;" />

<img src="assets/%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83/image-20250621142014077.png" alt="image-20250621142014077" style="zoom:25%;" />

<img src="assets/%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83/image-20250621142054025.png" alt="image-20250621142054025" style="zoom:25%;" />

---

## 四、CentOS 网络配置

### 1. 编辑网卡配置文件

进入网卡配置目录：

```bash
cd /etc/sysconfig/network-scripts/
```

编辑网卡配置文件（ens33 可能根据系统不同而变）：

```bash
vi ifcfg-ens33
```

配置为静态 IP，例如：

```
TYPE=Ethernet
BOOTPROTO=static//注意由dhcp修改为static
NAME=ens33
DEVICE=ens33
ONBOOT=yes//由no改为yes
IPADDR=192.168.100.100
NETMASK=255.255.255.0
GATEWAY=192.168.100.2
DNS1=8.8.8.8
```

保存退出后，重启网络服务：

```bash
service network restart
```

