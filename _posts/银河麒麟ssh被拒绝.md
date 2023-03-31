# 银河麒麟V10安装SSH连接被拒绝

## 问题描述

国产[Linux](https://www.cfnotes.com/tags/linux)操作系统[银河麒麟](https://www.cfnotes.com/tags/kylin-os)V10 SP1 2203 x86-64刚完成安装，检查了一下openssh-server是已经安装了的，然后用[SSH](https://www.cfnotes.com/tags/ssh)客户端进行链接时提示“Network error: Connection refused”，开始以为是防火墙的原因，关闭了防火墙仍然提示错误。

## 问题解决

后来发现，原因是ssh相关的文件权限导致的，解决方案如下：在[银河麒麟](https://www.cfnotes.com/tags/kylin-os)终端（需要接上鼠标显示器和键盘）中，执行如下命令：

```shell
cd /etc/ssh
sudo chmod 644 ./*
sudo chmod 600 ssh_host_dsa_key ssh_host_rsa_key
sudo chmod 755 .
sudo /etc/init.d/ssh restart
```

可以将以上命令拷贝一次性执行，拷贝的命令如下：

```shell
cd /etc/ssh && sudo chmod 644 ./* && sudo chmod 600 ssh_host_dsa_key ssh_host_rsa_key && sudo chmod 755 . && sudo /etc/init.d/ssh restart
```

执行完成以上命令后，就可以在[SSH](https://www.cfnotes.com/tags/ssh)客户端中进行连接了。

## 仍然无法连接

如果仍然 无法连接，并且报：Network error: Software caused connection abort
打开“安全中心”->“网络保护”，关闭联网控制，如下图：
![image-1661415813126](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/image-1661415813126.png)