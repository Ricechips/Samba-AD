# Samba-AD
域控

## 环境搭建
官方文档:[依赖](https://wiki.samba.org/index.php/Package_Dependencies_Required_to_Build_Samba#Red_Hat_Enterprise_Linux_7_.2F_CentOS_7_.2F_Scientific_Linux_7)
[搭建步骤](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)

>configure使报错:未找到GnuTLS(查询文档，明确要求要从外部获取)<br>
>*wget http://www.ring.gr.jp/pub/net/gnupg/gnutls/v3.6/gnutls-3.6.4.tar.xz* 编译报错:没有Nettle<br>
>*wget ftp://ftp.gnu.org/gnu/nettle/nettle-3.4.1.tar.gz* make报错:*make[1]: *** [aes-decrypt-internal.o] 错误 127*<br>
>起先以为是gcc没装好,反复重装和清除编译都无效,发现躲着一行*/bin/sh: m4: not found*,原来是m4这个包没装<br>
