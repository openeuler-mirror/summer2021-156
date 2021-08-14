# OpenStack-Train 迁移至 openEuler 指导

- [软件介绍](#软件介绍)
- [环境配置](#环境配置)
- [系统配置](#系统配置)
- [软件编译](#软件编译)
- [执行 devstack 脚本安装 OpenStack](#执行_devstack_脚本安装_OpenStack)
- [软件运行](#软件运行)
- [软件卸载](#软件卸载)
- [FAQ](#FAQ)
- [附录](#附录)

## 软件介绍

### OpenStack 简介

OpenStack 是一个社区，也是一个项目。它提供了一个部署云的操作平台或工具集，为组织提供可扩展的、灵活的云计算。

作为一个开源的云计算管理平台，OpenStack 由nova、neutron、glance、keystone、horizon等几个主要的组件组合起来完成具体工作。OpenStack 支持几乎所有类型的云环境，项目目标是提供实施简单、可大规模扩展、丰富、标准统一的云计算管理平台。OpenStack 通过各种互补的服务提供了基础设施即服务（IaaS）的解决方案，每个服务提供 API 进行集成。

### 适配版本

本文使用“Train”版本进行适配，OpenStack Train版本于2019年10月16日发布，是部署最广泛的开源云基础设施软件的第20个版本。

### DevStack 介绍

DevStack 是一组模块化脚本，运行这些脚本可以使开发人员快速轻松部署 OpenStack。这些脚本可以在裸机或虚拟机的单个节点上运行，也可以部署到多个节点。

DevStack 默认会安装 OpenStack 的核心服务，用户也可以修改配置文件来部署其他服务。通常，DevStack 从 git master 中拉取核心服务，也可以修改配置文件从稳定分支（stable branch）（如 stable/pike）克隆。

所有服务均从源安装，我们可以从[devstack.github](https://github.com/OpenStack/devstack )获取源。

本文使用 DevStack 脚本进行安装部署和测试，采用单机“All In One”模式，按照 CPU 架构不同，可以安装在 x86 或者 ARM 上。两者主要的安装步骤相同，仅有部分命令或者步骤有差异，具体差异点本文会有详细描述。





## 环境配置

建议部署环境内存大于 2 G。
### 软件平台

|  软件名称   |版本号  |安装方法   | 备注  |
|:---  |:----  |:----  |:----  |
| openEuler | 20.03-LTS-SP2 |iso  | x86可以选择虚拟机或物理机部署，ARM只能在物理机部署 |
| gcc | 7.3.0 |见必要库和依赖安装  |  |
| python3 | 3.7.9 |见必要库和依赖安装 |  |
| bash | 5.0 |见必要库和依赖安装 |  |
| devstack | Latest |见修改 devstack 脚本和安装配置 | 详见[devstack](https://github.com/OpenStack/devstack) |

### 必要依赖包

|  软件名称   |版本号  |安装方法   |
|:---  |:----  |:----  |
| python3-systemd | 234 |见必要库和依赖安装  |
| pcp-system-tools | 4.1.3  |见必要库和依赖安装  |
| haproxy | 2.0.14 |见必要库和依赖安装 |
| httpd httpd-devel | 2.4.43 |见必要库和依赖安装 |
| memcached | 1.5.10 |见必要库和依赖安装 |
| python3-devel | 3.7.9 |见必要库和依赖安装 |
| libffi-devel | 3.3.7 |见必要库和依赖安装 |
| open-iscsi-devel | 2.1.1 |见必要库和依赖安装 |
| libxml2 libxml2-devel python3-libxml2 | 2.9.10 |见必要库和依赖安装 |
| python3-lxml | 4.2.3 |见必要库和依赖安装 |
| libxslt libxslt-devel | 1.1.34 |见必要库和依赖安装 |
| edk2-ovmf(x86) edk2-aarch64(ARM) edk2-devel python3-edk2-devel | 202002 |见必要库和依赖安装 |
| qemu qemu-guest-agent | 4.1.0 |见必要库和依赖安装 |
| libvirt*  python3-libvirt | 6.2.0 |见必要库和依赖安装 |
| rabbitmq-server | 3.7.23 |见必要库和依赖安装 |
| python3-copr | 1.105 |见必要库和依赖安装 |
| python3-uWSGI | 2.0.19 |见必要库和依赖安装 |
| python3-mod_wsgi | 4.6.4 |见必要库和依赖安装 |
| python3-sqlalchemy python3-sqlalchemy-utils | 1.2.19 |见必要库和依赖安装 |
| python3-scss | 1.3.5 |见必要库和依赖安装 |
| openeuler-lsb | 5.0 |见必要库和依赖安装 |
| mariadb-server | 10.3.9 |见必要库和依赖安装 |


## 系统配置

### 关闭防火墙

1. 执行以下命令，停止防火墙。

    ```
    # systemctl stop firewalld.service
    ```

2. 执行以下命令，关闭防火墙。

    ```
    # systemctl disable firewalld.service
    ```

### 修改SELINUX为disabled

执行以下命令，关闭 SELINUX。

```
# sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
# setenforce 0
```

## 软件编译

### 必要库和依赖安装（本地yum源）

>![](./public_sys-resources/icon-note.gif) **说明：**   
>本节内容可以通过执行自动化脚本prep_install.sh实现，详见附录。

执行以下命令，安装脚本执行过程中所需的必要库和依赖。

```
# yum -y install tar git bash
# yum -y install rust
# yum -y install python3-systemd
# yum -y install libffi-devel
# yum -y install open-iscsi-devel
# yum -y install libxml2-devel 
# yum -y install python3-lxml python3-libxml2 libxslt libxslt-devel
# yum -y install pcp-system-tools
# yum -y install haproxy
# yum -y install qemu qemu-guest-agent
# yum -y install libvirt*  python3-libvirt
# yum -y install httpd httpd-devel
# yum -y install memcached
# yum -y install mariadb-server
# yum -y install rabbitmq-server
# yum -y install python3-uWSGI
# yum -y install python3-mod_wsgi 
# yum -y install python3-copr
# yum -y install python3-scss
# yum -y install gcc-c++
# yum -y install python3-devel
# yum -y install python3-sqlalchemy python3-sqlalchemy-utils
# yum -y install openeuler-lsb
```
利用 yum 源，安装 uefi 相关库，按照 CPU 架构不同，命令分别如下。

* x86 架构
```
# yum -y install edk2-ovmf edk2-devel  python3-edk2-devel
```
* ARM 架构
```
# yum -y install edk2-aarch64 edk2-devel  python3-edk2-devel
```


### 创建执行用户

1. 使用root用户登录待安装主机，执行以下命令创建 stack 用户来执行脚本。
    ```
    # useradd -s /bin/bash -d /home/stack -m stack
    ```

2. 执行以下操作，为 stack 用户设置 root 用户权限，后续操作使用 stack 用户操作。

    ```
    # chmod +w /etc/sudoers
    # vi /etc/sudoers //在sudoers文件的“root ALL=(ALL) ALL”下面，加入如下内容:stack  ALL=(ALL) NOPASSWD: ALL
    # chmod -w /etc/sudoers
    ```
    ![](./figures/createuser.png)

### 下载 devstack 脚本

切换 stack 用户，执行以下命令，下载 devstack 脚本文件:

```
# su - stack
# git clone -b stable/train https://opendev.org/OpenStack/devstack  
```
以下操作均使用 stack 用户执行。

1. 修改/home/stack/devstack/files/rpms/nova，将其中的mysql-devel 修改成mariadb-devel

2. 修改/home/stack/devstack/files/rpms/neutron-common，将其中的mysql-devel 修改成mariadb-devel

3. 修改/home/stack/devstack/files/rpms/general，将其中的redhat-rpm-config 修改成openEuler-rpm-config

4. 修改/home/stack/devstack/files/rpms/dstat,删除其中的dstat行

5. 修改/home/stack/devstack/lib/nova_plugins/functions-libvirt,将其中80行的install_package qemu-kvm修改为install_package qemu

6. 修改/home/stack/devstack/stackrc,第130行，修改后如下

   ```
   130 export USE_PYTHON3=$(trueorfalse True USE_PYTHON3)
   ```

7. 修改/home/stack/devstack/lib/apache,修改126行，128行

   ```
   126 uwsgi=$(ls uWSGI*)
   127 tar xvf $uwsgi
   128 cd ./apache2
   ```

8. 修改/home/stack/devstack/lib/lvm，注释130行,增加131行，修改后如下

   ```
   130 #start service lvm2_lvmetad
   131 sleep 1
   ```

9. 查看默认python版本，如果不是3.7.9，则修改为3.7.9

   ```
   cd /usr/bin
   sudo rm -rf python
   sudo ln -s /usr/bin/python3.7 /usr/bin/python
   ```



### 修改主机相关环境

>![](./public_sys-resources/icon-note.gif) **说明：**   
>本节内容可以通过执行自动化脚本prep_install.sh实现，详见附录。

1. 执行`sudo vi /etc/httpd/conf/httpd.conf`命令，使用管理员权限在 `/etc/httpd/conf/httpd.conf` 文件中增加如下配置，使之可以加载第三方插件服务，插入位置见下图。

    ```
	LoadModule wsgi_module modules/mod_wsgi_python3.so
	```
    ![](./figures/host_env1.png)

2. 执行如下命令，修正 yum 安装 edk2.x86_64 （ARM 架构的安装 edk2.aarch64）相关库时的bug，注意目录及文件相关权限。
    * x86 架构
        ```
	    # cd /usr/share
        # sudo mkdir OVMF && sudo chmod -R 755 OVMF
        # cd OVMF
        # sudo ln -s ../edk2/ovmf/OVMF_CODE.fd OVMF_CODE.fd
        # sudo ln -s ../edk2/ovmf/OVMF_VARS.fd OVMF_VARS.fd
	    ```

    * ARM 架构
        ```
		# cd /usr/share
        # sudo mkdir AAVMF && sudo chmod -R 755 AAVMF
        # cd AAVMF
        # sudo ln -s ../edk2/aarch64/QEMU_EFI-pflash.raw AAVMF_CODE.fd
        # sudo ln -s ../edk2/aarch64/vars-template-pflash.raw AAVMF_VARS.fd
	    ```

3. 在 `/etc/libvirt/qemu.conf` 文件中增加如下配置，增加 qemu 对 uefi 的支持。

    * x86 架构
        ```
        nvram = ["/usr/share/OVMF/OVMF_CODE.fd:/usr/share/OVMF/OVMF_VARS.fd","/usr/share/edk2/ovmf/OVMF_CODE.fd:/usr/share/edk2/ovmf/OVMF_VARS.fd"]
	    ```

    * ARM 架构
        ```
        nvram = ["/usr/share/AAVMF/AAVMF_CODE.fd:/usr/share/AAVMF/AAVMF_VARS.fd","/usr/share/edk2/aarch64/QEMU_EFI-pflash.raw:/usr/share/edk2/aarch64/vars-template-pflash.raw"]
	    ```


### 修改devstack脚本和相关配置

1. 执行以下命令，创建 `local.conf` 文件。

    ```
    # cd /home/stack/devstack
    # touch local.conf 
    ```
2. 编辑 `local.conf` 文件，配置如下内容。

    * x86 架构
        ```
        [[local|localrc]]
        HOST_IP=172.168.132.11    ///主机ip
        ADMIN_PASSWORD=a123456        ///各模块服务密码
        DATABASE_PASSWORD=d123456
        RABBIT_PASSWORD=r123456
        SERVICE_PASSWORD=s123456

        disable_service tempest       ///默认关闭测试模块的加载

        GIT_BASE=http://git.trystack.cn                   ///国内git源，用来下载OpenStack组件
        NOVNC_REPO=http://git.trystack.cn/kanaka/noVNC.git
        SPICE_REPO=http://git.trystack.cn/git/spice/spice-html5.git

        LOGFILE=$DEST/logs/stack.sh.log
		
	    ```

    * ARM 架构
        ```
        [[local|localrc]]
        HOST_IP=192.168.122.8        ///主机ip
        ADMIN_PASSWORD=a123456        ///各模块服务密码
        DATABASE_PASSWORD=d123456
        RABBIT_PASSWORD=r123456
        SERVICE_PASSWORD=s123456

        disable_service tempest       ///默认关闭测试模块的加载

        GIT_BASE=http://git.trystack.cn                   ///国内git源，用来下载OpenStack组件
        NOVNC_REPO=http://git.trystack.cn/kanaka/noVNC.git
        SPICE_REPO=http://git.trystack.cn/git/spice/spice-html5.git

        DOWNLOAD_DEFAULT_IMAGES=False    ///修改mirros镜像地址，默认下载aarch64镜像
        IMAGE_URLS="https://github.com/cirros-dev/cirros/releases/download/0.5.1/cirros-0.5.1-aarch64-disk.img"
        ETCD_DOWNLOAD_LOCATION=https://mirrors.huaweicloud.com/etcd/v3.3.12/etcd-v3.3.12-linux-arm64.tar.gz                 ///改用huaweicloud，减少脚本耗时

        LOGFILE=$DEST/logs/stack.sh.log

	    ```
>![](./public_sys-resources/icon-note.gif) **说明：**   
>本节以下内容可以通过执行自动化脚本prep_install.sh实现，详见附录。
3. 编辑 `/home/stack/devstack/stackrc` 文件，修改下图所示字段值为 stable/train，指定待安装的 OpenStack 的版本。

    ![](./figures/host_env5.png)

4. devstack 维护的平台暂不包含 openEuler，执行以下命令，适配 openEuler 版本安装方法。

  ```
    # cd /home/stack/devstack
    # sed -i "/\# Git Functions/i\\function is_openeuler {\n\tif [[ -z \"\$os_VENDOR\" ]]; then\n\tGetOSVersion\n\tfi\n\n\t[[ \"\$os_VENDOR\" =~ (openEuler) ]]\n}\n" functions-common
    # sed -i "s/elif is_fedora/elif is_fedora || is_openeuler/g" functions-common
    # sed -i "/DISTRO=\"f\$os_RELEASE\"/a\ \ \ \ elif [[ \"\$os_VENDOR\" =~ (openEuler) ]]; then\n\tDISTRO=\"openEuler-\$os_RELEASE\"" functions-common
    # grep -nir "is_fedora" | grep -v functions-common | cut -d ":" -f1 | sort | uniq | for line in `xargs`;do sed -i "s/is_fedora/is_fedora || is_openeuler/g" $line;done
  ```

5. 由于脚本文件中默认的 python-libvirt 版本不适配，需编辑 `/home/stack/devstack/lib/nova_plugins/functions-libvirt` 文件，注释掉安装 python-libvirt 相关代码。python-libvirt 已在openEuler-20.03-LTS-SP2 的 yum 源中手动安装。

    ![](./figures/host_env7.png)
	
6. 编辑`/home/stack/devstack/inc/python` 文件，修改`cmd_pip` 参数，参数值修改为使用国内源，如下图所示。

    ![](./figures/host_env8.png)

7. 修改 `/home/stack/devstack/inc/python` 文件，默认安装 glance 组件。

    ![](./figures/host_env9.png)

8. 修改`/home/stack/devstack/lib/neutron_plugins/services/l3` 文件，在图示位置添加如下配置。

    ```
    # source openrc admin admin
    ```

    ![](./figures/host_env10.png)

9. 修改`/home/stack/devstack/stackrc` 文件，修改 `VIRTUALENV_CMD` 参数值。

    ![](./figures/host_env11.png)

    修改完成后，保存退出，并执行以下命令：
	```
    # pip3 install virtualenv
    ```

## 执行 devstack 脚本安装 OpenStack

以stack用户，执行以下命令，运行 stack.sh 脚本，进行 OpenStack 单机版安装。

```
# cd  /home/stack/devstack
# FORCE=yes ./stack.sh 
```

安装过程大约需要十几分钟，x86架构安装成功显示信息与ARM架构一致，此处以安装ARM架构为例，安装成功页面如下图所示。

![](./figures/stack.png)

## 软件运行

devstack.sh 若执行成功，会在当前主机内，根据 local.conf 文件中的配置信息，安装指定的子模块，若 local.conf 中没有指定模块，则会安装所有子模块。

以 stack 用户执行以下命令，使用管理员登录 OpenStack 客户端。

```
# source openrc admin admin
```

* 获取相关资源列表

    - 执行以下命令，可以获取镜像资源列表。
	    ```
        # openstack image list
        ```
    - 执行以下命令，可以获取网络资源列表。
	    ```
        # openstack network list
        ```
    - 执行以下命令，可以获取虚拟机配置类型列表。
	    ```
        # openstack flavor list
        ```


* 启动一个实例

    - 使用查询到的资源，执行以下命令创建虚拟机。
	    - x86 架构
            ```
            # openstack server create --image cirros-0.5.1-x86_64-disk --flavor 1 vm
            ```
		
            ![](./figures/startvm.png)
		- ARM 架构
            ```
            # openstack server create --image cirros-0.5.1-aarch64-disk.img --flavor 1 vm
            ```
    - 执行如下命令，查看虚拟机状态。

        ```
        # openstack server list   //查看虚拟机状态
        ```
        
		![](./figures/vmlist.png)
			
		​	
## 软件卸载

1. 分别执行以下命令，卸载并清理 devstack 生成的文件及环境配置。

    ```
    # cd /home/stack/devstack

    # ./unstack.sh

    # ./clean.sh
    ```

2.  删除 devstack。

    ```
    # cd /home/stack 
    # rm -rf devstack
    # rm -rf /opt/stack
    ```


## FAQ

### openstack project list 因为网络问题有概率性失败

**问题现象**

脚本执行 `openstack project list` 命令报错。

**问题原因**

网络原因，执行完命令 `source openrc admin admin` 后，需要等待一段时间，再执行命令 `openstack project list` 才生效。

**解决方法**

参考下图修改 `/home/stack/devstack/lib/neutron\_plugins/services/l3` 文件。

![](./figures/faq1.png)


### devstack@q-meta.service 服务概率性启动失败

**问题现象**

命令 `sudo systemctl start devstack@q-meta.service` 执行失败。

**问题原因**

执行 `systemctl enable devstack@q-meta.service` 命令后，要等待一段时间。

**解决方法**

服务 enable 后，等待 30s 再启动。

参考下图修改 `/home/stack/devstack/functions-common` 文件。

![](./figures/zh-cn_image_0296837434.png)

### mariadb 服务启动失败

**问题现象**

mariadb 服务启动失败。

**问题原因**

mysql_install_db 数据库创建失败，提示gssapi插件报错、inodb建立失败、galgare地址失效等问题。

**解决方法**

由于没有使用到 gssapi插件，执行如下命令，卸载 mariadb-gssapi-server 包。
```
# ./unstack.sh ./clean.sh && FORCE=yes ./stack.sh
```


### neutron 服务启动失败

**问题现象**

neutron 服务启动过程中，有概率启动失败。

**问题原因**

网络波动，导致network节点搭建失败。

**解决方法**

执行如下命令，重新执行脚本。
```
# ./unstack.sh && FORCE=yes ./stack.sh
```

### pip引导失败

**问题现象**

pip 引导失败，控制台报错信息为 "ERROR: Links are not allowed as constraints"。

**问题原因**

pip 社区更新至20.3，版本不适配。

**解决方法**

删除/opt/stack/requirement下旧的python 虚拟运行环境，参考社区解决方案 ，使用补丁修改 devstack 源码。
在 /home/stack/devstack 目录下，执行如下命令：

```
# wget https://github.com/openstack/devstack/commit/7a3a7ce87.patch 
# sudo yum install patch -y  
# patch -p1 < 7a3a7ce87.patch
```

## 附录

自动化脚本 prep_install.sh点击[prep_install.sh](https://gitee.com/openeuler/docs/blob/stable2-20.03_LTS_SP2/docs/zh/docs/thirdparty_migration/prep_install.sh)获取。
将脚本存放到`/home/stack`目录，执行命令 `bash -x prep_install.sh`即可完成必要库和依赖安装、修改主机相关环境和修改devstack脚本和相关配置的部分操作。