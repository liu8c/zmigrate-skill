Preload知识点
什么是Preload？
在来源主机上对使用内核添加目标平台的驱动。
Preload支持哪些系统？
Linux系列的操作系统，如Redhat、CentOS、SuSE等。
什么场景要用Preload？
	•	软件暂未完全适配的Linux系统。
	•	部分厂商OEM的Linux系统。
	•	如：某监控厂商使用CentOS7做了一个操作系统，修改了系统版本相关的配置文件。
	•	/etc/redhat-release
	•	/etc/centos-release
	•	/etc/os-release
	•	手工编译的内核，名字存在自定义的字段。
	•	切换到可以识别的内核安装tarsync。
	•	对手动编译的内核做preload。
	•	迁移完成后再切换会期望内核。
怎么用Preload？
	•	根据操作系统版本手动执行对应系统的preload命令。
	•	使用Preload脚本。
使用Preload+WRK完成迁移

z: cd <软件目录> Server名称.exe --attach Server名称.exe --flush wpeutil.exe reboot 
