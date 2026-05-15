有不正确的Changed Block Tracking设定
问题描述

	•	VADP增量时，报VMware磁盘*vmdk有不正确的Changed Block Tracking设定。
问题分析
	•	CBT出错。
	•	本次错误原因：ESXi有强制重启。
	•	用户侧可能的原因不明。
解决方法
	•	关闭虚拟机电源。
	•	右键单击虚拟机，然后单击编辑设置。
	•	单击选项选项卡。
	•	单击“高级”区域下方的常规，然后单击配置参数。此时将打开“配置参数”对话框。
	•	将所需 SCSI 磁盘的 ctkEnabled 参数设置为 false。

	•	scsi0:0.ctkEnabled参数设置为false，如有多个scsi*:*.ctkEnabled，需全部改成false。

	•	打开虚拟机电源。
	•	在MGMT中点击同步（选择第二个完整数据复制）。

重置CBT参考资料
https://cloudbase.it/vmware-vm-cbt-reset-guide/
https://kb.vmware.com/s/article/2139574
