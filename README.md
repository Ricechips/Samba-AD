# Samba-AD
域控

## 环境搭建
官方文档:[依赖](https://wiki.samba.org/index.php/Package_Dependencies_Required_to_Build_Samba#Red_Hat_Enterprise_Linux_7_.2F_CentOS_7_.2F_Scientific_Linux_7)
[搭建步骤](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)

>configure使报错:未找到GnuTLS(查询文档，明确要求要从外部获取)<br>
>*wget http://www.ring.gr.jp/pub/net/gnupg/gnutls/v3.6/gnutls-3.6.4.tar.xz* 编译报错:没有Nettle<br>
>*wget ftp://ftp.gnu.org/gnu/nettle/nettle-3.4.1.tar.gz* make报错:*make[1]: *** [aes-decrypt-internal.o] 错误 127*<br>
>起先以为是gcc没装好,反复重装和清除编译都无效,发现躲着一行*/bin/sh: m4: not found*,原来是m4这个包没装<br>
>[教程](https://blog.csdn.net/HideInTime/article/details/94600815)


## 一段漫长又辛酸的搭建失败之反复重复失败直至成功史
>一开始接到任务时是先找了这篇[教程](http://lihaitao.cn/?p=299)进行搭建,然后就一直卡在Kerberos的kdc出不来,而且在/etc/resolv.conf配置这一环节也有莫名其妙的问题,疯狂查资料,国内查到国外,发现大家的做法都大同小异,在kdc那步都一笔带过,那我就很郁闷了。<br><br>
>后来跟着官方文档走,想说应该没问题了,结果在交互式时报错(libgnutls.so.30)找不到,我寻思我却是也没装这个,而是用的gnutls-devel阿,在rpmfind找这个包,发现没有centos7版本的,8和6都有,gg。<br><br>
>反复思考到底哪里出了问题,会不会是我本身系统部署了太多环境(cacti,opennebula...)然后什么东西冲突了?会不会是sssd服务被我搞坏了?算了,我重装一个干干净净的centos试试好了(虚拟机克隆半分钟出一台),对了,Samba的版本!一开始是用的4.1.12,后来用最新的4.12和4.10,这次搞下4.4.10好了。<br><br>
>成了,一路顺畅没有任何报错,起飞。
