## 第1页

1 VMWare 环境无代理模式经验汇总
1.1 架构和性能建议
 基础架构：使用无代理模式来源端必须准备 TARServer 来调用 VADP 端口。
 从复制性能最优的角度，如果 vsphere 版本是 7 及以上，建议每个 ESXi
上部署 1 台 TARServer，该 TARServer 只调用自身所在的物理 ESXi 的 VADP。
从性价比角度，建议 1 台 TARServer 对应 3~5 台 ESXi。
 复制并发数
 默认每台 TARServer 对应的每个 ESXi，最多同时只能运行 1 个复制任
务，其余的任务需要等待，以避免影响 ESXi 上的生产运行。
 如果多台虚机分别在不同 ESXi 上，则可以同时并发复制。
 有特殊需求时可酌情调整并发数的参数。详见 1.4
 可以使用多台 Mgmt 服务器，各自管理不同的 TARServer 和 ESXi 来提
升并发数
 对于灾备项目，复制间隔＜30 分钟，使用 TARSync 方式添加来源主机更
佳。间隔较大且虚机数量不多，则可以使用 API 方式添加注册。
 性能和稳定性建议
 读取方面，由于 vmware 自身的原因，vsphere6.5 及以下版本，使用 vadp
备份数据有概率出现 esxi 卡顿的情况，建议使用 TARSync。Vsphere6.7
及以上没有这个问题。
 写入方面，实测 Vsphere6.7 的 vadp 写入性能最好，Vsphere7、8 的性
能差一些，在意复制速度的话建议不使用 vadp 的方式写入目标：
 注册 VC 还是 ESXi

## 第2页

如果 ESXi 和虚机数量较多（大于 20 台），建议注册 ESXi，如果数量少，
则可以选择注册 VC。
 VMware 是目标云时，从快照稳定性角度，建议每台目标机的快照数为 3
个，最多不要超过 10 个。
1.2 查看当前 TARServer 的 VDDK 版本
为确保 VADP 读写的稳定性和最佳性能，建议调整对应版本的 VDDK 文件。
切换到 TARServer 的安装目录，例如：C:\Program Files\NaviClouDR\TARServer
查看 vmware-vdiskmanager 的详细信息即可。

## 第3页

 兼容列表
部分版本可能会有兼容性错误，更换成对应版本的 VDDK 后可解决。
VADP version ESXi version
7.0 6.5、6.7、7.0(P.S. 1)
6.7 6.0、6.5、6.7 (P.S. 2) 、7.0.3(sh)
6.5 5.5、6.0、6.5
6.0 5.1、5.5、6.0
1.3 升级/降级 VDDK 到指定版本
 适用情况
Windows TARServer
VADP 写入失败
 准备工作
登录到 TARServer 的操作系统中。
下载 VDDK 文件并上传到 TARServer 的操作系统中。
需下载 Windows 版本 VDDK。
VMware 官网下载：
下载 VDDK7.0.3.2：VMware vSphere Virtual Disk Development Kit 7.0.3.2
下载 VDDK6.7.1：VMware vSphere Virtual Disk Development Kit 6.7U1
下载 VDDK6.5.4：VMware vSphere Virtual Disk Development Kit 6.5.4
下载 VDDK6.0.2：VMware vSphere Virtual Disk Development Kit 6.0.2
 升级步骤
1) 停止 TARServer.service。
2) 备份 TARServer 文件夹。
3) 解压 VDDK 压缩包。

## 第4页

4) 把 VDDK 文件夹中 bin 文件夹中的所有文件复制到 TARServer 文件夹，选择替换目
标中的文件。
5) 启动 TARServer.service。
6) 升级/降级完成。
1.4 修改 TARServer 并发 VADP 任务数量
 写在前面
默认 VADP 的复制任务，同一个 ESXi 只能跑一个复制 Job。
这个设定是为了限制 vadp 的并发数，避免影响生产业务，修改前需与用户做好
沟通工作。

## 第5页

 可能存在的报错信息
等待可用的 CPU 资源以开始复制程序。
 操作步骤
1) 停止 TARServer.service。
2) 修改注册表，把任务并发数量从 1 改为自定义。建议不超过 3 个。
3) 启动 TARServer.service。

## 第6页

说明：
来源平台是 VADP 读取，则修改来源 TARServer 的注册表。
目标平台是 VADP 写入，则修改目标 TARServer 的注册表。
来源和目标平台都是 VADP 读取与写入，则修改来源和目标 TARServer 的注
册表。
1.5 其他注意事项
 某些类型的磁盘无法通过 VADP 读取：无法读取 VMDK 磁盘 xxx.vmdk
 “RDM、独立-持久、独立-非持久”类型磁盘不支持 vadp
 “从属”类型磁盘支持 vadp
 有时候复制报虚机快照失败：
 先检查来源虚机是否可以手动拍快照，如果不行，需要从 VMware 层
面修复快照功能
 检查虚机是否有安装 vmtools，如果装了，重启一下 vmtools 服务后再
复制，如果重启也不行，关闭或卸载 vmtools 后再复制
 如果用户不愿意删除或禁用 vmtools，或禁用后还是失败，则建议检查
虚机里是否有灾备或备份软件，一些软件会阻止虚机拍摄静默快照，
禁用这些软件后正常。
 有些环境里从 VC 读取时会等待 10 分钟：
这是 VMware 自身的机制导致，疑似该虚机含 vmotion 状态标签，一般较
难修复，如果不想等待，可以选择从 ESXi 注册读取。
 复制时虚机数据读取失败
如果多台都是这种情况，可能是 VDDK 兼容性不佳，参考 1.2 和 1.3 节
替换合适的 VDDK 后再次重建复制。
