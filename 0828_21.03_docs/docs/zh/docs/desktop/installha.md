# HA的安装与部署

本文介绍如何安装和部署HA高可用集群。


## 安装与部署

### 环境准备

需要至少两台安装了openEuler 21.03 的物理机/虚拟机（现以两台为例），安装方法参考《安装指南》。

### 修改主机名称及/etc/hosts文件

**注：两台主机均需要进行以下操作，现以其中一台为例，下文中使用的IP仅供参考。**

在使用HA软件之前，需要确认修改主机名并将所有主机名写入/etc/hosts文件中。
1. 修改主机名
    ```
    # hostnamectl set-hostname ha1
    ```

2. 编辑`/etc/hosts`文件并写入以下字段
    ```
    172.30.30.65 ha1
    172.30.30.66 ha2
    ```

### 配置yum源
成功安装系统后，会默认配置好yum源，文件位置存放在`/etc/yum.repos.d/openEuler.repo`文件中，HA软件包会用到以下源：
```
[OS]
name=OS
baseurl=http://repo.openeuler.org/openEuler-21.03/OS/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://repo.openeuler.org/openEuler-21.03/OS/$basearch/RPM-GPG-KEY-openEuler

[everything]
name=everything
baseurl=http://repo.openeuler.org/openEuler-21.03/everything/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://repo.openeuler.org/openEuler-21.03/everything/$basearch/RPM-GPG-KEY-openEuler

[EPOL]
name=EPOL
baseurl=http://repo.openeuler.org/openEuler-21.03/EPOL/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://repo.openeuler.org/openEuler-21.03/OS/$basearch/RPM-GPG-KEY-openEuler
```

### 安装HA软件包组件
```
# yum install -y corosync pacemaker pcs fence-agents fence-virt corosync-qdevice sbd drbd drbd-utils
```

### 设置hacluster用户密码
```
# passwd hacluster
```

### 修改`/etc/corosync/corosync.conf`文件
```
totem {
        version: 2
        cluster_name: hacluster
         crypto_cipher: none
        crypto_hash: none
}
logging {         
        fileline: off
        to_stderr: yes
        to_logfile: yes
        logfile: /var/log/cluster/corosync.log
        to_syslog: yes
        debug: on
       logger_subsys {
               subsys: QUORUM
               debug: on
        }
}
quorum {
           provider: corosync_votequorum
           expected_votes: 2
           two_node: 1
       }
nodelist {
       node {
               name: ha1
               nodeid: 1
               ring0_addr: 172.30.30.65
               }
        node {
               name: ha2
               nodeid: 2
               ring0_addr: 172.30.30.66
               }
        }
```
### 管理服务

#### 关闭防火墙

1. 执行如下命令，关闭防火墙。
    ```
    # systemctl stop firewalld
    ```
2. 修改`/etc/selinux/config`文件中SELINUX状态为disabled。
    ```
    # SELINUX=disabled
    ```

#### 管理pcs服务
1. 启动pcs服务：
    ```
    # systemctl start pcsd
    ```

2. 查询pcs服务状态：
    ```
    # systemctl status pcsd
    ```

    若回显为如下，则服务启动成功。

    ![](./figures/HA-pcs.png)

#### 管理pacemaker服务
1. 启动pacemaker服务：
    ```
    # systemctl start pacemaker
    ```

2. 查询pacemaker服务状态：
    ```
    # systemctl status pacemaker
    ```

    若回显为如下，则服务启动成功。

    ![](./figures/HA-pacemaker.png)

#### 管理corosync服务
1. 启动corosync服务：
    ```
    # systemctl start corosync
    ```

2. 查询corosync服务状态：
    ```
    # systemctl status corosync
    ```

    若回显为如下，则服务启动成功。

    ![](./figures/HA-corosync.png)

### 节点鉴权
**注：任选一个节点上执行即可**
```
# pcs host auth ha1 ha2
```

### 访问前端管理平台

上述服务启动成功后，打开浏览器（建议使用：Chrome，Firfox），在浏览器导航栏中输入`https://localhost:2224`即可。
-  以下界面为原生管理平台

![](./figures/HA-login.png)

若安装社区新开发的管理平台请参考文档`https://gitee.com/openeuler/ha-api/blob/master/docs/build.md`
-  以下为社区新开发的管理平台

![](./figures/HA-api.png)

想了解如何快速使用HA高可用集群，以及添加一个实例。请参考[HA的使用实例文档](./HA的使用实例.md)。