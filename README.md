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
![avatar](https://github.com/Ricechips/Samba-AD/blob/master/PrtScn/11.PNG)


## 命令
```c
/usr/local/samba/etc/smb.conf 文件共享目录及配置
/usr/local/samba/sbin/samba  启动samba   (killall samba)
```
## 最后的最后
>成功部署了samba+ad+dc后，用的管理工具是windows自带的RSAT（具体来说是Active Directory Users and Computers）<br><br>
>新建了一台准备入狱（域）的win10,dns调好后发现用用户的帐号密码怎么登都报错（计算机名错误），很奇怪，后使用administrator入域成功，切换用户。（原来本来加域流程就是要这么走的）<br><br>
>在samba端创建一share文件夹并设好权限,管理员访问文件ok，用户怎么都访问不了（明明文件夹在管理员界面权限已经开放），后发现是samba那边share虽然设好了，但其上级/home/aoc/share权限问题。<br><br>
![avatar](https://github.com/Ricechips/Samba-AD/blob/master/PrtScn/Screenshot%20from%202020-06-24%2017-42-34.png)

## 其实还没有结束
>因为部署成功的版本是：centos7.8 + samba4.4  （3年前的了） 所以要升级<br>
>现在的版本：centos8.2 + samba4.12 (2020)<br>
>部署过程：
```c
/etc/sysconfig/network-scripts 找到网卡进行配置
nmcli c reload 重启服务上网
dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm -y 加epel仓库
dnf repolist epel -v 查看版本
echo centos8-samba > /etc/hostname  重启生效
# yum -y install dnf-plugins-core
# yum config-manager --set-enabled PowerTools 开启PowerTools存储库
# yum install docbook-style-xsl gcc gdb gnutls-devel gpgme-devel jansson-devel \
      keyutils-libs-devel krb5-workstation libacl-devel libaio-devel \
      libarchive-devel libattr-devel libblkid-devel libtasn1 libtasn1-tools \
      libxml2-devel libxslt lmdb-devel openldap-devel pam-devel perl \
      perl-ExtUtils-MakeMaker perl-Parse-Yapp popt-devel python3-cryptography \
      python3-dns python3-gpg python36-devel readline-devel rpcgen systemd-devel \
      tar zlib-devel   安装依赖
wget https://download.samba.org/pub/samba/stable/samba-4.12.3.tar.gz
tar -zxvf samba-4.1.12.tar.gz
cd samba-4.1.12
./configure && make && make install 编译安装
/usr/local/samba/bin/samba-tool domain provision --use-rfc2307 --interactive 交互式配置
cat /usr/local/samba/etc/smb.conf 配置信息
/usr/local/samba/sbin/samba  开启
/usr/local/samba/bin/smbclient -L localhost -U% 
echo “nameserver 127.0.0.1” > /etc/resolv.conf
/usr/local/samba/bin/samba-tool dns zonelist 127.0.0.1 -U administrator
cp /usr/local/samba/private/krb5.conf /etc/krb5.conf
kinit administrator@TESTAD.LOCAL
```
>windows ipv4的dns设成域服务器并加域<br>
>linux共享目录映射到windows下磁盘
![avatar](https://github.com/Ricechips/Samba-AD/blob/master/PrtScn/IMG_4709.JPG)
