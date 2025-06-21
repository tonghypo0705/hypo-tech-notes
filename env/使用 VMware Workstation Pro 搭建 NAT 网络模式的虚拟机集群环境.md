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

![image-20250621112408741](%E4%BD%BF%E7%94%A8%20VMware%20Workstation%20Pro%20%E6%90%AD%E5%BB%BA%20NAT%20%E7%BD%91%E7%BB%9C%E6%A8%A1%E5%BC%8F%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%9B%86%E7%BE%A4%E7%8E%AF%E5%A2%83.assets/image-20250621112408741.png)

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

```ini
TYPE=Ethernet
BOOTPROTO=static
NAME=ens33
DEVICE=ens33
ONBOOT=yes
IPADDR=192.168.100.100
NETMASK=255.255.255.0
GATEWAY=192.168.100.2
DNS1=8.8.8.8
```

保存退出后，重启网络服务：

```bash
service network restart
```

> 💡 如果你使用的是 `NetworkManager`，可以改用 `nmcli` 工具进行配置。

---

## 五、VMware NAT 设置说明（可选端口映射）

打开 VMware 菜单：  
“编辑” → “虚拟网络编辑器” → 管理 VMnet8 设置：

- 确保启用 DHCP
- 配置子网（如：192.168.100.0/24）
- 设置网关地址（如：192.168.100.2）
- 添加端口转发（如把主机 2222 端口转发到虚拟机 22 端口）

示例端口映射表：

| 协议 | 主机端口 | 虚拟机 IP        | 虚拟机端口 |
|------|----------|------------------|------------|
| TCP  | 2222     | 192.168.100.100  | 22         |

---

## 六、测试验证

### 1. 虚拟机访问外网

在虚拟机中执行：

```bash
ping www.baidu.com
```

如果能通说明 NAT 上网成功。

### 2. 主机访问虚拟机

通过端口映射访问虚拟机 SSH：

```bash
ssh root@127.0.0.1 -p 2222
```

---

## 七、总结

通过本文搭建的 NAT 虚拟机环境，具备如下优势：

- 虚拟机可访问外网，适合拉取依赖和更新
- 主机与虚拟机通信简便
- 可用于构建小型本地分布式集群环境

后续你可以基于此环境部署 Zookeeper、Kafka、Hadoop 等服务，完成本地集群测试。

---

## 八、附录：常见问题排查

### 虚拟机无法联网？
- 检查网卡名称是否正确（`ip a` 查看）
- 检查 `ifcfg-ens33` 文件内容是否有误
- 检查是否启用了防火墙（可暂时关闭 `systemctl stop firewalld`）

### 主机无法连接虚拟机？
- 检查 VMware 的 NAT 设置是否添加了端口映射
- 检查虚拟机是否开启 SSH 服务

---

> 📌 本文参考了内部文档《VM虚拟机的NAT模式安装》，并结合实际操作经验编写。
