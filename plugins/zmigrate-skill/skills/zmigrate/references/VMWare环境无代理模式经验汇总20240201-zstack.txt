VMWare环境无代理模式经验汇总
本文仅讨论来源是VMware的场景

架构和性能建议
	•	基础架构：使用无代理模式来源端必须准备Treker来调用VADP端口。
	•	从复制性能最优的角度，如果vsphere版本是7及以上，建议每个ESXi上部署1台Treker，该Treker只调用自身所在的物理ESXi的VADP。从性价比角度，建议1台Treker对应3~5台ESXi。
	•	复制并发数
	•	默认每台Treker对应的每个ESXi，最多同时只能运行1个复制任务，其余的任务需要等待，以避免影响ESXi上的生产运行。
	•	如果多台虚机分别在不同ESXi上，则可以同时并发复制。
	•	有特殊需求时可酌情调整并发数的参数。详见1.4
	•	可以使用多台Mgmt服务器，各自管理不同的Treker和ESXi来提升并发数
	•	对于灾备项目，复制间隔＜30分钟，使用Antenna方式添加来源主机更佳。间隔较大且虚机数量不多，则可以使用API方式添加注册。
	•	性能和稳定性建议
	•	读取方面，由于vmware自身的原因，vsphere6.5及以下版本，使用vadp备份数据有概率出现esxi卡顿的情况，建议使用Antenna。Vsphere6.7及以上没有这个问题。
	•	写入方面，实测Vsphere6.7的vadp写入性能最好，Vsphere7、8的性能差一些，在意复制速度的话建议不使用vadp的方式写入目标。
	•	注册VC还是ESXi
如果ESXi和虚机数量较多（大于20台），建议注册ESXi，如果数量少，则可以选择注册VC。无论是注册vc还是ESXi，mgmt和Treker都必须可以访问虚机所在的vc和ESXi的443、902端口。
	•	VMware是目标云时，从快照稳定性角度，建议每台目标机的快照数为3个，最多不要超过10个。


查看当前Treker的VDDK版本
为确保VADP读写的稳定性和最佳性能，建议调整对应版本的VDDK文件。
切换到Treker的安装目录，例如：C:\Program Files\Zstack\Treker
查看vmware-vdiskmanager的详细信息即可。


	•	兼容列表
部分版本可能会有兼容性错误，更换成对应版本的VDDK后可解决。
VADP version 
ESXi version 
7.0 
6.5、6.7、7.0(P.S. 1) 
6.7 
6.0、6.5、6.7 (P.S. 2) 、7.0.3(sh)
6.5 
5.5、6.0、6.5 
6.0 
5.1、5.5、6.0 


升级/降级VDDK到指定版本
	•	适用情况
Windows Treker
VADP写入失败
	•	准备工作
登录到Treker的操作系统中。
下载VDDK文件并上传到Treker的操作系统中。
需下载Windows版本VDDK。
VMware官网下载：
下载VDDK7.0.3.2：VMware vSphere Virtual Disk Development Kit 7.0.3.2
下载VDDK6.7.1：VMware vSphere Virtual Disk Development Kit 6.7U1
下载VDDK6.5.4：VMware vSphere Virtual Disk Development Kit 6.5.4
下载VDDK6.0.2：VMware vSphere Virtual Disk Development Kit 6.0.2

	•	升级步骤
	•	停止Treker.service。

	•	备份Treker文件夹。

	•	解压VDDK压缩包。

	•	把VDDK文件夹中bin文件夹中的所有文件复制到Treker文件夹，选择替换目标中的文件。
	•	启动Treker.service。

	•	升级/降级完成。

修改Treker并发VADP任务数量
	•	写在前面
默认VADP的复制任务，同一个ESXi只能跑一个复制Job。
这个设定是为了限制vadp的并发数，避免影响生产业务，修改前需与用户做好沟通工作。
	•	可能存在的报错信息
等待可用的CPU资源以开始复制程序。


	•	操作步骤
	•	停止Treker.service。
	•	修改注册表，把任务并发数量从1改为自定义。建议不超过3个。


	•	启动Treker.service。

说明：
来源平台是VADP读取，则修改来源Treker的注册表。
目标平台是VADP写入，则修改目标Treker的注册表。
来源和目标平台都是VADP读取与写入，则修改来源和目标Treker的注册表。

其他注意事项
	•	某些类型的磁盘无法通过VADP读取：无法读取VMDK磁盘xxx.vmdk
	•	“RDM、独立-持久、独立-非持久”类型磁盘不支持vadp
	•	“从属”类型磁盘支持vadp

	•	有时候复制报虚机快照失败：

	•	先检查来源虚机是否可以手动拍快照，如果不行，需要从VMware层面修复快照功能

	•	检查虚机是否有安装vmtools，如果装了，重启一下vmtools服务后再复制，如果重启也不行，关闭或卸载vmtools后再复制
	•	如果用户不愿意删除或禁用vmtools，或禁用后还是失败，则建议检查虚机里是否有灾备或备份软件，一些软件会阻止虚机拍摄静默快照，禁用这些软件后正常。参考知识库《vadp来源复制报“无法在虚机创建目标快照”》

	•	有些环境里从VC读取时会等待10分钟：
这是VMware自身的机制导致，疑似该虚机含vmotion状态标签，一般较难修复，如果不想等待，可以选择从ESXi注册读取。

	•	复制时虚机数据读取失败
如果多台都是这种情况，可能是VDDK兼容性不佳，参考1.2和1.3节替换合适的VDDK后再次重建复制。

