

# OpenStack-Victoria 部署指南

## OpenStack 简介

OpenStack 是一个社区，也是一个项目。它提供了一个部署云的操作平台或工具集，为组织提供可扩展的、灵活的云计算。

作为一个开源的云计算管理平台，OpenStack 由nova、cinder、neutron、glance、keystone、horizon等几个主要的组件组合起来完成具体工作。OpenStack 支持几乎所有类型的云环境，项目目标是提供实施简单、可大规模扩展、丰富、标准统一的云计算管理平台。OpenStack 通过各种互补的服务提供了基础设施即服务（IaaS）的解决方案，每个服务提供 API 进行集成。

openEuler 21.03 版本的官方 yum 源已经支持 Openstack-Victoria 版本，用户可以配置好官方 yum 源后根据此文档进行 OpenStack 部署。


## 准备环境
### 环境配置

在`/etc/hosts`中添加controller信息，例如节点IP是`10.0.0.11`，则新增：

```shell
10.0.0.11   controller
```

### 安装 SQL DataBase

1. 执行如下命令，安装软件包。

    ```plain
    # yum install mariadb mariadb-server python-PyMySQL
    ```
2. 执行如下命令，创建并编辑 `/etc/my.cnf.d/openstack.cnf` 文件。
    ```
    vim /etc/my.cnf.d/openstack.cnf
    ```
	复制如下内容到文件，其中 bind-address 设置为控制节点的管理IP地址。
    ```
    [mysqld]
    bind-address = 10.0.0.11
    default-storage-engine = innodb
    innodb_file_per_table = on
    max_connections = 4096
    collation-server = utf8_general_ci
    character-set-server = utf8
    ```

3. 启动 DataBase 服务，并为其配置开机自启动：

    ```
    # systemctl enable mariadb.service
    # systemctl start mariadb.service
    ```
	
### 安装 RabbitMQ 

1. 执行如下命令，安装软件包。

    ```
    #yum install rabbitmq-server
    ```

2. 启动 RabbitMQ 服务，并为其配置开机自启动。

    ```
    #systemctl enable rabbitmq-server.service
    #systemctl start rabbitmq-server.service
    ```
3. 添加 OpenStack用户。

    ```
    #rabbitmqctl add_user openstack RABBIT_PASS
    ```
4. 替换 RABBIT_PASS，为OpenStack用户设置密码

5. 设置openstack用户权限，允许进行配置、写、读：

    ```
    #rabbitmqctl set_permissions openstack ".*" ".*" ".*"
    ```

### 安装 Memcached 

1. 执行如下命令，安装依赖软件包。

    ```
    #yum install memcached python3-memcached
    ```
2. 执行如下命令，编辑 `/etc/sysconfig/memcached` 文件。

    ```
    #vim /etc/sysconfig/memcached
    OPTIONS="-l 127.0.0.1,::1,controller"
    ```
    OPTIONS 修改为实际环境中控制节点的管理IP地址。

3. 执行如下命令，启动 Memcached 服务，并为其配置开机启动。

    ```
    # systemctl enable memcached.service
    # systemctl start memcached.service
    ```

## 安装 OpenStack

### 安装 Keystone

1. 以 root 用户访问数据库，创建 keystone 数据库并授权。

    ```
    # mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE keystone;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
    IDENTIFIED BY 'KEYSTONE_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
    IDENTIFIED BY 'KEYSTONE_DBPASS';
    MariaDB [(none)]> exit
    ```
    替换 KEYSTONE_DBPASS，为 Keystone 数据库设置密码

2. 执行如下命令，安装软件包。

    ```
    #yum install openstack-keystone httpd mod_wsgi
    ```
3. 配置keystone，编辑 `/etc/keystone/keystone.conf` 文件。在[database]部分，配置数据库入口。在[token]部分，配置token provider

    ```
    # vim /etc/keystone/keystone.conf
    [database]
    connection = mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone
    [token]
    provider = fernet
    ```
    替换KEYSTONE_DBPASS为Keystone数据库的密码

4. 执行如下命令，同步数据库。

    ```
    su -s /bin/sh -c "keystone-manage db_sync" keystone
    ```
5. 执行如下命令，初始化Fernet密钥仓库。

    ```
    # keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
    # keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
    ```
6. 执行如下命令，启动身份服务。

    ```
    # keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
    --bootstrap-admin-url http://controller:5000/v3/ \
    --bootstrap-internal-url http://controller:5000/v3/ \
    --bootstrap-public-url http://controller:5000/v3/ \
    --bootstrap-region-id RegionOne
    ```
    替换 ADMIN_PASS，为 admin 用户设置密码。

7. 编辑 `/etc/httpd/conf/httpd.conf` 文件，配置Apache HTTP server

    ```
    #vim /etc/httpd/conf/httpd.conf
    ```

    配置 ServerName 项引用控制节点，如下所示。
    ```
    ServerName controller
    ```
	
    如果 ServerName 项不存在则需要创建。

8. 执行如下命令，为 `/usr/share/keystone/wsgi-keystone.conf` 文件创建链接。

    ```
    #ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/

    #vim /etc/httpd/conf.d/wsgi-keystone.conf
    ```

9. 完成安装，执行如下命令，启动Apache HTTP服务。

    ```
    # systemctl enable httpd.service
    # systemctl start httpd.service
    ```

10. 执行如下命令，设置环境变量。

    ```
    $ export OS_USERNAME=admin
    $ export OS_PASSWORD=ADMIN_PASS
    $ export OS_PROJECT_NAME=admin
    $ export OS_USER_DOMAIN_NAME=Default
    $ export OS_PROJECT_DOMAIN_NAME=Default
    $ export OS_AUTH_URL=http://controller:5000/v3
    $ export OS_IDENTITY_API_VERSION=3
    ```
    替换 ADMIN_PASS 为 keystone-manage bootstrap 命令中设置的密码

11. 分别执行如下命令，创建domain, projects, users, roles。

    创建domain ‘example’：

    ```
    $ openstack domain create --description "An Example Domain" example
    ```

    注：domain ‘default’在 keystone-manage bootstrap 时已创建

    创建project ‘service’：

    ```
    $ openstack project create --domain default --description "Service Project" service
    ```

    创建（non-admin）project ’myproject‘，user ’myuser‘ 和 role ’myrole‘，为‘myproject’和‘myuser’添加角色‘myrole’：

    ```
    $ openstack project create --domain default --description "Demo Project" myproject
    $ openstack user create --domain default --password-prompt myuser
    $ openstack role create myrole
    $ openstack role add --project myproject --user myuser myrole
    ```
12. 验证

    取消临时环境变量OS_AUTH_URL和OS_PASSWORD：

    ```
    $ unset OS_AUTH_URL OS_PASSWORD
    ```

    为admin用户请求token：

    ```
    $ openstack --os-auth-url http://controller:5000/v3 \
    --os-project-domain-name Default --os-user-domain-name Default \
    --os-project-name admin --os-username admin token issue
    ```

    为myuser用户请求token：

    ```
    $ openstack --os-auth-url http://controller:5000/v3 \
    --os-project-domain-name Default --os-user-domain-name Default \
    --os-project-name myproject --os-username myuser token issue
    ```

13. 创建 OpenStack client 环境脚本

    分别为admin和demo用户创建环境变量脚本：

    ```
    # vim admin-openrc
    export OS_PROJECT_DOMAIN_NAME=Default
    export OS_USER_DOMAIN_NAME=Default
    export OS_PROJECT_NAME=admin
    export OS_USERNAME=admin
    export OS_PASSWORD=ADMIN_PASS
    export OS_AUTH_URL=http://controller:5000/v3
    export OS_IDENTITY_API_VERSION=3
    export OS_IMAGE_API_VERSION=2
    #
    ```
    ```
    # vim demo-openrc
    export OS_PROJECT_DOMAIN_NAME=Default
    export OS_USER_DOMAIN_NAME=Default
    export OS_PROJECT_NAME=myproject
    export OS_USERNAME=myuser
    export OS_PASSWORD=DEMO_PASS
    export OS_AUTH_URL=http://controller:5000/v3
    export OS_IDENTITY_API_VERSION=3
    export OS_IMAGE_API_VERSION=2
    ```
    替换ADMIN_PASS为admin用户的密码

    替换DEMO_PASS为myuser用户的密码

    运行脚本加载环境变量：

    ```
    $ source admin-openrc
    ```

### Glance安装

1. 创建数据库、服务凭证和 API 端点

    创建数据库：

    以 root 用户访问数据库，创建 glance 数据库并授权。

    ```
    $ mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE glance;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
    IDENTIFIED BY 'GLANCE_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
    IDENTIFIED BY 'GLANCE_DBPASS';
    MariaDB [(none)]> exit
    ```
	
    替换 GLANCE_DBPASS，为 glance 数据库设置密码。

    ```
    $ source admin-openrc
    ```
    
	执行以下命令，分别完成创建 glance 服务凭证、创建glance用户和添加‘admin’角色到用户‘glance’。

	```
	$ openstack user create --domain default --password-prompt glance
	$ openstack role add --project service --user glance admin
	$ openstack service create --name glance --description "OpenStack Image" image
	```
	创建镜像服务API端点：

	```
	$ openstack endpoint create --region RegionOne image public http://controller:9292
	$ openstack endpoint create --region RegionOne image internal http://controller:9292
	$ openstack endpoint create --region RegionOne image admin http://controller:9292
	```

2. 安装和配置

	安装软件包：

	```
	#yum install openstack-glance openstack-glance-api
	```
	配置glance：

	编辑 /etc/glance/glance-api.conf 文件：

	在[database]部分，配置数据库入口

	在[keystone_authtoken] [paste_deploy]部分，配置身份认证服务入口

	在[glance_store]部分，配置本地文件系统存储和镜像文件的位置

	```
	# vim /etc/glance/glance-api.conf
	[database]
	# ...
	connection = mysql+pymysql://glance:GLANCE_DBPASS@controller/glance
	[keystone_authtoken]
	# ...
	www_authenticate_uri  = http://controller:5000
	auth_url = http://controller:5000
	memcached_servers = controller:11211
	auth_type = password
	project_domain_name = Default
	user_domain_name = Default
	project_name = service
	username = glance
	password = GLANCE_PASS
	[paste_deploy]
	# ...
	flavor = keystone
	[glance_store]
	# ...
	stores = file,http
	default_store = file
	filesystem_store_datadir = /var/lib/glance/images/
	```
	
	其中，替换 GLANCE_DBPASS 为 glance 数据库的密码，替换 GLANCE_PASS 为 glance 用户的密码。

	同步数据库：

	```
	su -s /bin/sh -c "glance-manage db_sync" glance
	```
	启动镜像服务：

	```
	# systemctl enable openstack-glance-api.service
	# systemctl start openstack-glance-api.service
	```
3. 验证

	下载镜像
	```
	$ source admin-openrc
    # 注意：如果您使用的环境是鲲鹏架构，请下载arm64版本的镜像。
    $ wget http://download.cirros-cloud.net/0.4.0/cirros-0.4.0-x86_64-disk.img
   ```

    向Image服务上传镜像：

    ```
    $ glance image-create --name "cirros" --file cirros-0.4.0-x86_64-disk.img --disk-format qcow2 --container-format bare --visibility=public
    ```

    确认镜像上传并验证属性：

    ```
    $ glance image-list
    ```
### Placement安装

1. 创建数据库、服务凭证和 API 端点

    创建数据库：

    作为 root 用户访问数据库，创建 placement 数据库并授权。

    ```
    $ mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE placement;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' \
    IDENTIFIED BY 'PLACEMENT_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' \
    IDENTIFIED BY 'PLACEMENT_DBPASS';
    MariaDB [(none)]> exit
    ```
    替换 PLACEMENT_DBPASS，为 placement 数据库设置密码

    ```
    $ source admin-openrc
    ```
    执行如下命令，创建 placement 服务凭证、创建 placement 用户以及添加‘admin’角色到用户‘placement’。

    创建Placement API服务

    ```
    $ openstack user create --domain default --password-prompt placement
    $ openstack role add --project service --user placement admin
    $ openstack service create --name placement --description "Placement API" placement
    ```
    创建placement服务API端点：

    ```
    $ openstack endpoint create --region RegionOne placement public http://controller:8778
    $ openstack endpoint create --region RegionOne placement internal http://controller:8778
    $ openstack endpoint create --region RegionOne placement admin http://controller:8778
    ```
2. 安装和配置

    安装软件包：

    ```
    yum install openstack-placement-api
    ```
    配置placement：

    编辑 /etc/placement/placement.conf 文件：

    在[placement_database]部分，配置数据库入口

    在[api] [keystone_authtoken]部分，配置身份认证服务入口

    ```
    # vim /etc/placement/placement.conf
    [placement_database]
    # ...
    connection = mysql+pymysql://placement:PLACEMENT_DBPASS@controller/placement
    [api]
    # ...
    auth_strategy = keystone
    [keystone_authtoken]
    # ...
    auth_url = http://controller:5000/v3
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = placement
    password = PLACEMENT_PASS
    ```
    其中，替换 PLACEMENT_DBPASS 为 placement 数据库的密码，替换 PLACEMENT_PASS 为 placement 用户的密码。

    同步数据库：

    ```
    #su -s /bin/sh -c "placement-manage db sync" placement
    ```
    启动httpd服务：

    ```
    #systemctl restart httpd
    ```
3. 验证

    执行如下命令，执行状态检查：
    ```
    $ . admin-openrc
    $ placement-status upgrade check
    ```

    安装osc-placement，列出可用的资源类别及特性：

    ```
    $ yum install python3-osc-placement
    $ openstack --os-placement-api-version 1.2 resource class list --sort-column name
    $ openstack --os-placement-api-version 1.6 trait list --sort-column name
    ```
### Nova 安装

1. 创建数据库、服务凭证和 API 端点

    创建数据库：

    作为root用户访问数据库，创建nova、nova_api、nova_cell0 数据库并授权

    ```
    $ mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE nova_api;
    MariaDB [(none)]> CREATE DATABASE nova;
    MariaDB [(none)]> CREATE DATABASE nova_cell0;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' \
    IDENTIFIED BY 'NOVA_DBPASS';
    MariaDB [(none)]> exit
    ```
    替换NOVA_DBPASS，为nova数据库设置密码

    执行如下命令，完成创建nova服务凭证、创建nova用户以及添加‘admin’角色到用户‘nova’。

    ```
    $ . admin-openrc
    $ openstack user create --domain default --password-prompt nova
    $ openstack role add --project service --user nova admin
    $ openstack service create --name nova --description "OpenStack Compute" compute
    ```

    创建计算服务API端点：

    ```
    $ openstack endpoint create --region RegionOne compute public http://controller:8774/v2.1
    $ openstack endpoint create --region RegionOne compute internal http://controller:8774/v2.1
    $ openstack endpoint create --region RegionOne compute admin http://controller:8774/v2.1
    ```

2. 安装和配置

    安装软件包：

    ```
    # yum install openstack-nova-api openstack-nova-conductor \
    openstack-nova-novncproxy openstack-nova-scheduler openstack-nova-compute
    ```

    配置nova：

    编辑 /etc/nova/nova.conf 文件：

    在[default]部分，启用计算和元数据的API，配置RabbitMQ消息队列入口，配置my_ip；

    在[api_database] [database]部分，配置数据库入口；

    在[api] [keystone_authtoken]部分，配置身份认证服务入口；

    在[vnc]部分，启用并配置远程控制台入口；

    在[glance]部分，配置镜像服务API的地址；

    在[oslo_concurrency]部分，配置lock path；

    在[placement]部分，配置placement服务的入口。

    ```
    # vim /etc/nova/nova.conf
    [DEFAULT]
    # ...
    enabled_apis = osapi_compute,metadata
    transport_url = rabbit://openstack:RABBIT_PASS@controller:5672/
    my_ip = 10.0.0.11
    [api_database]
    # ...
    connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova_api
    [database]
    # ...
    connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova
    [api]
    # ...
    auth_strategy = keystone
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000/
    auth_url = http://controller:5000/
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = Default
    user_domain_name = Default
    project_name = service
    username = nova
    password = NOVA_PASS
    [vnc]
    enabled = true
    # ...
    server_listen = $my_ip
    server_proxyclient_address = $my_ip
    novncproxy_base_url = http://controller:6080/vnc_auto.html
    [glance]
    # ...
    api_servers = http://controller:9292
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/nova/tmp
    [placement]
    # ...
    region_name = RegionOne
    project_domain_name = Default
    project_name = service
    auth_type = password
    user_domain_name = Default
    auth_url = http://controller:5000/v3
    username = placement
    password = PLACEMENT_PASS
    [neutron]
    # ...
    auth_url = http://controller:5000
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    region_name = RegionOne
    project_name = service
    username = neutron
    password = NEUTRON_PASS
    ```

    替换RABBIT_PASS为RabbitMQ中openstack账户的密码；

    配置my_ip为控制节点的管理IP地址；

    替换NOVA_DBPASS为nova数据库的密码；

    替换NOVA_PASS为nova用户的密码；

    替换PLACEMENT_PASS为placement用户的密码；

    替换NEUTRON_PASS为neutron用户的密码；

    同步nova-api数据库：

    ```
    su -s /bin/sh -c "nova-manage api_db sync" nova
    ```
    注册cell0数据库：

    ```
    su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova
    ```
    创建cell1 cell：

    ```
    su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova
    ```
    同步nova数据库：

    ```
    su -s /bin/sh -c "nova-manage db sync" nova
    ```
    验证cell0和cell1注册正确：

    ```
    su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova
    ```
    确定是否支持虚拟机硬件加速（x86架构）：

    ```
    $ egrep -c '(vmx|svm)' /proc/cpuinfo
    ```

    如果返回值为0则不支持硬件加速，需要配置libvirt使用QEMU而不是KVM：

    ```
    # vim /etc/nova/nova.conf
    [libvirt]
    # ...
    virt_type = qemu
    ```
    如果返回值为1或更大的值，则支持硬件加速，不需要进行额外的配置

    启动计算服务及其依赖项，并配置其开机启动：

    ```
    # systemctl enable \
    openstack-nova-api.service \
    openstack-nova-scheduler.service \
    openstack-nova-conductor.service \
    openstack-nova-novncproxy.service
    # systemctl start \
    openstack-nova-api.service \
    openstack-nova-scheduler.service \
    openstack-nova-conductor.service \
    openstack-nova-novncproxy.service
    ```
    ```
    # systemctl enable libvirtd.service openstack-nova-compute.service
    # systemctl start libvirtd.service openstack-nova-compute.service
    ```
    添加计算节点到cell数据库：

    确认计算节点存在：

    ```
    $ . admin-openrc
    $ openstack compute service list --service nova-compute
    ```
    注册计算节点：

    ```
    #su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova
    ```
3. 验证

    ```
    $ . admin-openrc
    ```
    列出服务组件，验证每个流程都成功启动和注册：

    ```
    $ openstack compute service list
    ```

    列出身份服务中的API端点，验证与身份服务的连接：

    ```
    $ openstack catalog list
    ```

    列出镜像服务中的镜像，验证与镜像服务的连接：

    ```
    $ openstack image list
    ```

    检查cells和placement API是否运作成功，以及其他必要条件是否已具备。

    ```
    #nova-status upgrade check
    ```
	
### Neutron安装

1. 创建数据库、服务凭证和 API 端点

    创建数据库：

    作为root用户访问数据库，创建 neutron 数据库并授权。

    ```
    $ mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE neutron;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
    IDENTIFIED BY 'NEUTRON_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
    IDENTIFIED BY 'NEUTRON_DBPASS';
    MariaDB [(none)]> exit
    ```
    替换NEUTRON_DBPASS，为neutron数据库设置密码。

    ```
    $ . admin-openrc
    ```
    执行如下命令，完成创建 neutron 服务凭证、创建neutron用户和添加‘admin’角色到‘neutron’用户操作。

    创建neutron服务

    ```
    $ openstack user create --domain default --password-prompt neutron
    $ openstack role add --project service --user neutron admin
    $ openstack service create --name neutron --description "OpenStack Networking" network
    ```
    创建网络服务API端点：

    ```
    $ openstack endpoint create --region RegionOne network public http://controller:9696
    $ openstack endpoint create --region RegionOne network internal http://controller:9696
    $ openstack endpoint create --region RegionOne network admin http://controller:9696
    ```
2. 安装和配置 Self-service 网络

    安装软件包：

    ```
    # yum install openstack-neutron openstack-neutron-ml2 \
    openstack-neutron-linuxbridge ebtables ipset
    ```
    配置neutron：

    编辑 /etc/neutron/neutron.conf 文件：

    在[database]部分，配置数据库入口；

    在[default]部分，启用ml2插件和router插件，允许ip地址重叠，配置RabbitMQ消息队列入口；

    在[default] [keystone]部分，配置身份认证服务入口；

    在[default] [nova]部分，配置网络来通知计算网络拓扑的变化；

    在[oslo_concurrency]部分，配置lock path。

    ```
    # vim /etc/neutron/neutron.conf
    [database]
    # ...
    connection = mysql+pymysql://neutron:NEUTRON_DBPASS@controller/neutron
    [DEFAULT]
    # ...
    core_plugin = ml2
    service_plugins = router
    allow_overlapping_ips = true
    transport_url = rabbit://openstack:RABBIT_PASS@controller
    auth_strategy = keystone
    notify_nova_on_port_status_changes = true
    notify_nova_on_port_data_changes = true
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000
    auth_url = http://controller:5000
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    project_name = service
    username = neutron
    password = NEUTRON_PASS
    [nova]
    # ...
    auth_url = http://controller:5000
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    region_name = RegionOne
    project_name = service
    username = nova
    password = NOVA_PASS
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/neutron/tmp
    ```
	
    替换NEUTRON_DBPASS为neutron数据库的密码；

    替换RABBIT_PASS为RabbitMQ中openstack账户的密码；

    替换NEUTRON_PASS为neutron用户的密码；
    
    替换NOVA_PASS为nova用户的密码。

    配置ML2插件：

    编辑 /etc/neutron/plugins/ml2/ml2_conf.ini 文件：

    在[ml2]部分，启用 flat、vlan、vxlan 网络，启用网桥及 layer-2 population 机制，启用端口安全扩展驱动；

    在[ml2_type_flat]部分，配置 flat 网络为 provider 虚拟网络；

    在[ml2_type_vxlan]部分，配置 VXLAN 网络标识符范围；

    在[securitygroup]部分，配置允许 ipset。

    ```
    # vim /etc/neutron/plugins/ml2/ml2_conf.ini
    [ml2]
    # ...
    type_drivers = flat,vlan,vxlan
    tenant_network_types = vxlan
    mechanism_drivers = linuxbridge,l2population
    extension_drivers = port_security
    [ml2_type_flat]
    # ...
    flat_networks = provider
    [ml2_type_vxlan]
    # ...
    vni_ranges = 1:1000
    [securitygroup]
    # ...
    enable_ipset = true
    ```
    配置 Linux bridge 代理：

    编辑 /etc/neutron/plugins/ml2/linuxbridge_agent.ini 文件：

    在[linux_bridge]部分，映射 provider 虚拟网络到物理网络接口；

    在[vxlan]部分，启用 vxlan 覆盖网络，配置处理覆盖网络的物理网络接口 IP 地址，启用 layer-2 population；

    在[securitygroup]部分，允许安全组，配置 linux bridge iptables 防火墙驱动。

    ```
    # vim /etc/neutron/plugins/ml2/linuxbridge_agent.ini
    [linux_bridge]
    physical_interface_mappings = provider:PROVIDER_INTERFACE_NAME
    [vxlan]
    enable_vxlan = true
    local_ip = OVERLAY_INTERFACE_IP_ADDRESS
    l2_population = true
    [securitygroup]
    # ...
    enable_security_group = true
    firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
    ```
    替换PROVIDER_INTERFACE_NAME为物理网络接口；

    替换OVERLAY_INTERFACE_IP_ADDRESS为控制节点的管理IP地址。

    配置Layer-3代理：

    编辑 /etc/neutron/l3_agent.ini 文件：

    在[default]部分，配置接口驱动为linuxbridge

    ```
    # vim /etc/neutron/l3_agent.ini
    [DEFAULT]
    # ...
    interface_driver = linuxbridge
    ```
    配置DHCP代理：

    编辑 /etc/neutron/dhcp_agent.ini 文件：

    在[default]部分，配置linuxbridge接口驱动、Dnsmasq DHCP驱动，启用隔离的元数据。

    ```
    # vim /etc/neutron/dhcp_agent.ini
    [DEFAULT]
    # ...
    interface_driver = linuxbridge
    dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
    enable_isolated_metadata = true
    ```
    配置metadata代理：

    编辑 /etc/neutron/metadata_agent.ini 文件：

    在[default]部分，配置元数据主机和shared secret。

    ```
    # vim /etc/neutron/metadata_agent.ini
    [DEFAULT]
    # ...
    nova_metadata_host = controller
    metadata_proxy_shared_secret = METADATA_SECRET
    ```
    替换METADATA_SECRET为合适的元数据代理secret。

3. 配置计算服务

    编辑 /etc/nova/nova.conf 文件：

    在[neutron]部分，配置访问参数，启用元数据代理，配置secret。

    ```
    # vim /etc/nova/nova.conf
    [neutron]
    # ...
    auth_url = http://controller:5000
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    region_name = RegionOne
    project_name = service
    username = neutron
    password = NEUTRON_PASS
    service_metadata_proxy = true
    metadata_proxy_shared_secret = METADATA_SECRET
    ```
    替换NEUTRON_PASS为neutron用户的密码；

    替换METADATA_SECRET为合适的元数据代理secret。

4. 完成安装

    添加链接：

    ```
    #ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini
    ```
    同步数据库：

    ```
    # su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
    --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron
    ```
    重启计算API服务：

    ```
    #systemctl restart openstack-nova-api.service
    ```
    启动网络服务并配置开机启动：

    ```
    # systemctl enable neutron-server.service \
    neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
    neutron-metadata-agent.service
    # systemctl start neutron-server.service \
    neutron-linuxbridge-agent.service neutron-dhcp-agent.service \
    neutron-metadata-agent.service
    # systemctl enable neutron-l3-agent.service
    # systemctl start neutron-l3-agent.service
    ```
5. 验证

    列出代理验证 neutron 代理启动成功：

    ```
    $ openstack network agent list
    ```
### Cinder 安装


1. 创建数据库、服务凭证和 API 端点

    创建数据库：

    作为root用户访问数据库，创建cinder数据库并授权。

    ```
    $ mysql -u root -p
    MariaDB [(none)]> CREATE DATABASE cinder;
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' \
    IDENTIFIED BY 'CINDER_DBPASS';
    MariaDB [(none)]> GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' \
    IDENTIFIED BY 'CINDER_DBPASS';
    MariaDB [(none)]> exit
    ```
    替换CINDER_DBPASS，为cinder数据库设置密码。

    ```
    $ source admin-openrc
    ```

    创建cinder服务凭证：

    创建cinder用户

    添加‘admin’角色到用户‘cinder’

    创建cinderv2和cinderv3服务

    ```
    $ openstack user create --domain default --password-prompt cinder
    $ openstack role add --project service --user cinder admin
    $ openstack service create --name cinderv2 --description "OpenStack Block Storage" volumev2
    $ openstack service create --name cinderv3 --description "OpenStack Block Storage" volumev3
    ```
    创建块存储服务API端点：

    ```
    $ openstack endpoint create --region RegionOne volumev2 public http://controller:8776/v2/%s
    $ openstack endpoint create --region RegionOne volumev2 internal http://controller:8776/v2/%s
    $ openstack endpoint create --region RegionOne volumev2 admin http://controller:8776/v2/%s
    $ openstack endpoint create --region RegionOne volumev3 public http://controller:8776/v3/%s
    $ openstack endpoint create --region RegionOne volumev3 internal http://controller:8776/v3/%s
    $ openstack endpoint create --region RegionOne volumev3 admin http://controller:8776/v3/%s
    ```
2. 安装和配置控制节点

    安装软件包：

    ```
    #yum install openstack-cinder
    ```
    配置cinder：

    编辑 /etc/cinder/cinder.conf 文件：

    在[database]部分，配置数据库入口；

    在[DEFAULT]部分，配置RabbitMQ消息队列入口，配置my_ip；

    在[DEFAULT] [keystone_authtoken]部分，配置身份认证服务入口；

    在[oslo_concurrency]部分，配置lock path。

    ```
    # vim /etc/cinder/cinder.conf
    [database]
    # ...
    connection = mysql+pymysql://cinder:CINDER_DBPASS@controller/cinder
    [DEFAULT]
    # ...
    transport_url = rabbit://openstack:RABBIT_PASS@controller
    auth_strategy = keystone
    my_ip = 10.0.0.11
    [keystone_authtoken]
    # ...
    www_authenticate_uri = http://controller:5000
    auth_url = http://controller:5000
    memcached_servers = controller:11211
    auth_type = password
    project_domain_name = default
    user_domain_name = default
    project_name = service
    username = cinder
    password = CINDER_PASS
    [oslo_concurrency]
    # ...
    lock_path = /var/lib/cinder/tmp
    ```
    替换CINDER_DBPASS为cinder数据库的密码；

    替换RABBIT_PASS为RabbitMQ中openstack账户的密码；

    配置my_ip为控制节点的管理IP地址；

    替换CINDER_PASS为cinder用户的密码；

    同步数据库：

    ```
    su -s /bin/sh -c "cinder-manage db sync" cinder
    ```
    配置计算使用块存储：

    编辑 /etc/nova/nova.conf 文件。

    ```
    # vim /etc/nova/nova.conf
    [cinder]
    os_region_name = RegionOne
    ```
    完成安装：

    重启计算API服务

    ```
    systemctl restart openstack-nova-api.service
    ```
    启动块存储服务

    ```
    # systemctl enable openstack-cinder-api.service openstack-cinder-scheduler.service
    # systemctl start openstack-cinder-api.service openstack-cinder-scheduler.service
    ```
3. 安装和配置存储节点

    安装软件包：

    ```
    yum install lvm2 device-mapper-persistent-data targetcli python-keystone
    ```
    启动服务：

    ```
    # systemctl enable lvm2-lvmetad.service
    # systemctl start lvm2-lvmetad.service
    ```
    创建LVM物理卷 /dev/sdb：

    ```
    pvcreate /dev/sdb
    ```
    创建LVM卷组 cinder-volumes：

    ```
    vgcreate cinder-volumes /dev/sdb
    ```
    编辑 /etc/lvm/lvm.conf 文件：

    在devices部分，添加过滤以接受/dev/sdb设备拒绝其他设备。

    devices {

    ...

    filter = [ "a/sdb/", "r/.*/"]

    编辑 /etc/cinder/cinder.conf 文件：

    在[lvm]部分，使用LVM驱动、cinder-volumes卷组、iSCSI协议和适当的iSCSI服务配置LVM后端。

    在[DEFAULT]部分，启用LVM后端，配置镜像服务API的位置。

    ```
    [lvm]
    volume_driver = cinder.volume.drivers.lvm.LVMVolumeDriver
    volume_group = cinder-volumes
    target_protocol = iscsi
    target_helper = lioadm
    [DEFAULT]
    # ...
    enabled_backends = lvm
    glance_api_servers = http://controller:9292
    ```
    完成安装：

    ```
    # systemctl enable openstack-cinder-volume.service target.service
    # systemctl start openstack-cinder-volume.service target.service
    ```
4. 安装和配置备份服务

    编辑 /etc/cinder/cinder.conf 文件：

    在[DEFAULT]部分，配置备份选项

    ```
    [DEFAULT]
    # ...
    # 注意: openEuler 21.03中没有提供OpenStack Swift软件包，需要用户自行安装。或者使用其他的备份后端，例如，NFS。NFS已经过测试验证，可以正常使用。
    backup_driver = cinder.backup.drivers.swift.SwiftBackupDriver
    backup_swift_url = SWIFT_URL
    ```
    替换SWIFT_URL为对象存储服务的URL，该URL可以通过对象存储API端点找到：

    ```
    $ openstack catalog show object-store
    ```
    完成安装：

    ```
    # systemctl enable openstack-cinder-backup.service
    # systemctl start openstack-cinder-backup.service
    ```
5. 验证

    列出服务组件验证每个步骤成功：
    ```
    $ source admin-openrc
    $ openstack volume service list
    ```
	
    注：目前暂未对swift组件进行支持，有条件的同学可以配置对接ceph。

### horizon 安装

1. 安装软件包

    ```plain
    yum install openstack-horizon
    ```
2. 修改文件`/usr/share/openstack-dashboard/openstack_dashboard/local/local_settings.py`
   
	修改变量 

    ```plain
    ALLOWED_HOSTS = ['*', ]OPENSTACK_HOST = "controller"OPENSTACK_KEYSTONE_URL = "http://%s:5000/v3" % OPENSTACK_HOST
    ```
    新增变量
    ```plain
    OPENSTACK_API_VERSIONS = {    "identity": 3,    "image": 2,    "volume": 3,}WEBROOT = "/dashboard/"COMPRESS_OFFLINE = TrueOPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "default"OPENSTACK_KEYSTONE_DEFAULT_ROLE = "admin"LOGIN_URL = '/dashboard/auth/login/'LOGOUT_URL = '/dashboard/auth/logout/'
    ```
3. 在/usr/share/openstack-dashboard目录下执行
    ```plain
    ./manage.py compress
    ```
4. 重启 httpd 服务
    ```plain
    systemctl restart httpd
    ```
5. 验证
    打开浏览器，输入网址http://<host_ip>，登录 horizon。

### Tempest 安装

Tempest是OpenStack的集成测试服务，如果用户需要全面自动化测试已安装的OpenStack环境的功能,则推荐使用该组件。否则，可以不用安装

1. 安装Tempest
    ```
    yum install openstack-tempest
    ```
2. 初始化目录

    ```
    tempest init mytest
    ```
3. 修改配置文件。

    ```
    cd mytest
    vi etc/tempest.conf
    ```
    tempest.conf中需要配置当前OpenStack环境的信息，具体内容可以参考[官方示例](https://docs.openstack.org/tempest/latest/sampleconf.html)

4. 执行测试

    ```
    tempest run
    ```

### Ironic 安装

Ironic是OpenStack的裸金属服务，如果用户需要进行裸机部署则推荐使用该组件。否则，可以不用安装。

1. 设置数据库

   裸金属服务在数据库中存储信息，创建一个**ironic**用户可以访问的**ironic**数据库，替换**IRONIC_DBPASSWORD**为合适的密码

   ```
   # mysql -u root -p MariaDB [(none)]> CREATE DATABASE ironic CHARACTER SET utf8; 
   
   MariaDB [(none)]> GRANT ALL PRIVILEGES ON ironic.* TO 'ironic'@'localhost' \     
   IDENTIFIED BY 'IRONIC_DBPASSWORD'; 
   
   MariaDB [(none)]> GRANT ALL PRIVILEGES ON ironic.* TO 'ironic'@'%' \     
   IDENTIFIED BY 'IRONIC_DBPASSWORD';
   ```

2. 组件安装与配置

   ##### 创建服务用户认证

   1、创建Bare Metal服务用户

   ```
   $ openstack user create --password IRONIC_PASSWORD \ 
   --email ironic@example.com ironic 
   $ openstack role add --project service --user ironic admin 
   $ openstack service create --name ironic --description \ 
   "Ironic baremetal provisioning service" baremetal 
   
   $ openstack service create --name ironic-inspector --description     "Ironic inspector baremetal provisioning service" baremetal-introspection 
   $ openstack user create --password IRONIC_INSPECTOR_PASSWORD --email ironic_inspector@example.com ironic_inspector 
   $ openstack role add --project service --user ironic-inspector admin
   ```

   2、创建Bare Metal服务访问入口

   ```
   $ openstack endpoint create --region RegionOne baremetal admin http://$IRONIC_NODE:6385 
   $ openstack endpoint create --region RegionOne baremetal public http://$IRONIC_NODE:6385 
   $ openstack endpoint create --region RegionOne baremetal internal http://$IRONIC_NODE:6385 
   $ openstack endpoint create --region RegionOne baremetal-introspection internal http://172.20.19.13:5050/v1 
   $ openstack endpoint create --region RegionOne baremetal-introspection public http://172.20.19.13:5050/v1 
   $ openstack endpoint create --region RegionOne baremetal-introspection admin http://172.20.19.13:5050/v1
   ```

   ##### 配置ironic-api服务

   配置文件路径/etc/ironic/ironic.conf

   1、通过**connection**选项配置数据库的位置，如下所示，替换**IRONIC_DBPASSWORD**为**ironic**用户的密码，替换**DB_IP**为DB服务器所在的IP地址：

   ```
   [database] 
   
   # The SQLAlchemy connection string used to connect to the 
   # database (string value) 
   
   connection = mysql+pymysql://ironic:IRONIC_DBPASSWORD@DB_IP/ironic
   ```

   2、通过以下选项配置ironic-api服务使用RabbitMQ消息代理，替换**RPC_\***为RabbitMQ的详细地址和凭证

   ```
   [DEFAULT] 
   
   # A URL representing the messaging driver to use and its full 
   # configuration. (string value) 
   
   transport_url = rabbit://RPC_USER:RPC_PASSWORD@RPC_HOST:RPC_PORT/
   ```

   用户也可自行使用json-rpc方式替换rabbitmq

   3、配置ironic-api服务使用身份认证服务的凭证，替换**PUBLIC_IDENTITY_IP**为身份认证服务器的公共IP，替换**PRIVATE_IDENTITY_IP**为身份认证服务器的私有IP，替换**IRONIC_PASSWORD**为身份认证服务中**ironic**用户的密码：

   ```
   [DEFAULT] 
   
   # Authentication strategy used by ironic-api: one of 
   # "keystone" or "noauth". "noauth" should not be used in a 
   # production environment because all authentication will be 
   # disabled. (string value) 
   
   auth_strategy=keystone 
   
   [keystone_authtoken] 
   # Authentication type to load (string value) 
   auth_type=password 
   # Complete public Identity API endpoint (string value) 
   www_authenticate_uri=http://PUBLIC_IDENTITY_IP:5000 
   # Complete admin Identity API endpoint. (string value) 
   auth_url=http://PRIVATE_IDENTITY_IP:5000 
   # Service username. (string value) 
   username=ironic 
   # Service account password. (string value) 
   password=IRONIC_PASSWORD 
   # Service tenant name. (string value) 
   project_name=service 
   # Domain name containing project (string value) 
   project_domain_name=Default 
   # User's domain name (string value) 
   user_domain_name=Default
   ```

   4、创建裸金属服务数据库表

   ```
   $ ironic-dbsync --config-file /etc/ironic/ironic.conf create_schema
   ```

   5、重启ironic-api服务

   ```
   sudo systemctl restart openstack-ironic-api
   ```

   ##### 配置ironic-conductor服务

   1、替换**HOST_IP**为conductor host的IP

   ```
   [DEFAULT] 
   
   # IP address of this host. If unset, will determine the IP 
   # programmatically. If unable to do so, will use "127.0.0.1". 
   # (string value) 
   
   my_ip=HOST_IP
   ```

   2、配置数据库的位置，ironic-conductor应该使用和ironic-api相同的配置。替换**IRONIC_DBPASSWORD**为**ironic**用户的密码，替换DB_IP为DB服务器所在的IP地址：

   ```
   [database] 
   
   # The SQLAlchemy connection string to use to connect to the 
   # database. (string value) 
   
   connection = mysql+pymysql://ironic:IRONIC_DBPASSWORD@DB_IP/ironic
   ```

   3、通过以下选项配置ironic-api服务使用RabbitMQ消息代理，ironic-conductor应该使用和ironic-api相同的配置，替换**RPC_\***为RabbitMQ的详细地址和凭证

   ```
   [DEFAULT] 
   
   # A URL representing the messaging driver to use and its full 
   # configuration. (string value) 
   
   transport_url = rabbit://RPC_USER:RPC_PASSWORD@RPC_HOST:RPC_PORT/
   ```

   用户也可自行使用json-rpc方式替换rabbitmq

   4、配置凭证访问其他OpenStack服务

   为了与其他OpenStack服务进行通信，裸金属服务在请求其他服务时需要使用服务用户与OpenStack Identity服务进行认证。这些用户的凭据必须在与相应服务相关的每个配置文件中进行配置。

   ```
   [neutron] - 访问Openstack网络服务 
   [glance] - 访问Openstack镜像服务 
   [swift] - 访问Openstack对象存储服务 
   [cinder] - 访问Openstack块存储服务 
   [inspector] - 访问Openstack裸金属introspection服务 
   [service_catalog] - 一个特殊项用于保存裸金属服务使用的凭证，该凭证用于发现注册在Openstack身份认证服务目录中的自己的API URL端点
   ```

   简单起见，可以对所有服务使用同一个服务用户。为了向后兼容，该用户应该和ironic-api服务的[keystone_authtoken]所配置的为同一个用户。但这不是必须的，也可以为每个服务创建并配置不同的服务用户。

   在下面的示例中，用户访问openstack网络服务的身份验证信息配置为：

   ```
   网络服务部署在名为RegionOne的身份认证服务域中，仅在服务目录中注册公共端点接口
   
   请求时使用特定的CA SSL证书进行HTTPS连接
   
   与ironic-api服务配置相同的服务用户
   
   动态密码认证插件基于其他选项发现合适的身份认证服务API版本
   ```

   ```
   [neutron] 
   
   # Authentication type to load (string value) 
   auth_type = password 
   # Authentication URL (string value) 
   auth_url=https://IDENTITY_IP:5000/ 
   # Username (string value) 
   username=ironic 
   # User's password (string value) 
   password=IRONIC_PASSWORD 
   # Project name to scope to (string value) 
   project_name=service 
   # Domain ID containing project (string value) 
   project_domain_id=default 
   # User's domain id (string value) 
   user_domain_id=default 
   # PEM encoded Certificate Authority to use when verifying 
   # HTTPs connections. (string value) 
   cafile=/opt/stack/data/ca-bundle.pem 
   # The default region_name for endpoint URL discovery. (string 
   # value) 
   region_name = RegionOne 
   # List of interfaces, in order of preference, for endpoint 
   # URL. (list value) 
   valid_interfaces=public
   ```

   默认情况下，为了与其他服务进行通信，裸金属服务会尝试通过身份认证服务的服务目录发现该服务合适的端点。如果希望对一个特定服务使用一个不同的端点，则在裸金属服务的配置文件中通过endpoint_override选项进行指定：

   ```
   [neutron] ... endpoint_override = <NEUTRON_API_ADDRESS>
   ```

   5、配置允许的驱动程序和硬件类型

   通过设置enabled_hardware_types设置ironic-conductor服务允许使用的硬件类型：

   ```
   [DEFAULT] enabled_hardware_types = ipmi 
   ```

   配置硬件接口：

   ```
   enabled_boot_interfaces = pxe enabled_deploy_interfaces = direct,iscsi enabled_inspect_interfaces = inspector enabled_management_interfaces = ipmitool enabled_power_interfaces = ipmitool
   ```

   配置接口默认值：

   ```
   [DEFAULT] default_deploy_interface = direct default_network_interface = neutron
   ```

   如果启用了任何使用Direct deploy的驱动，必须安装和配置镜像服务的Swift后端。Ceph对象网关(RADOS网关)也支持作为镜像服务的后端。

   6、重启ironic-conductor服务

   ```
   sudo systemctl restart openstack-ironic-conductor
   ```

   ##### 配置ironic-inspector服务

   配置文件路径/etc/ironic-inspector/inspector.conf

   1、创建数据库

   ```
   # mysql -u root -p 
   
   MariaDB [(none)]> CREATE DATABASE ironic_inspector CHARACTER SET utf8; 
   
   MariaDB [(none)]> GRANT ALL PRIVILEGES ON ironic_inspector.* TO 'ironic_inspector'@'localhost' \     IDENTIFIED BY 'IRONIC_INSPECTOR_DBPASSWORD'; 
   MariaDB [(none)]> GRANT ALL PRIVILEGES ON ironic_inspector.* TO 'ironic_inspector'@'%' \     
   IDENTIFIED BY 'IRONIC_INSPECTOR_DBPASSWORD';
   ```

   2、通过**connection**选项配置数据库的位置，如下所示，替换**IRONIC_INSPECTOR_DBPASSWORD**为**ironic_inspector**用户的密码，替换**DB_IP**为DB服务器所在的IP地址：

   ```
   [database] 
   backend = sqlalchemy 
   connection = mysql+pymysql://ironic_inspector:IRONIC_INSPECTOR_DBPASSWORD@DB_IP/ironic_inspector
   ```

   3、配置消息度列通信地址

   ```
   [DEFAULT] transport_url = rabbit://RPC_USER:RPC_PASSWORD@RPC_HOST:RPC_PORT/
   ```

   4、设置keystone认证

   ```
   [DEFAULT] 
   
   auth_strategy = keystone 
   
   [ironic] 
   
   api_endpoint = http://IRONIC_API_HOST_ADDRRESS:6385 
   auth_type = password 
   auth_url = http://PUBLIC_IDENTITY_IP:5000 
   auth_strategy = keystone 
   ironic_url = http://IRONIC_API_HOST_ADDRRESS:6385 
   os_region = RegionOne 
   project_name = service 
   project_domain_name = default 
   user_domain_name = default 
   username = IRONIC_SERVICE_USER_NAME 
   password = IRONIC_SERVICE_USER_PASSWORD
   ```

   5、配置ironic inspector dnsmasq服务

   ```
   # 配置文件地址：/etc/ironic-inspector/dnsmasq.conf 
   port=0 
   interface=enp3s0                         #替换为实际监听网络接口 
   dhcp-range=172.20.19.100,172.20.19.110   #替换为实际dhcp地址范围 
   bind-interfaces 
   enable-tftp 
   
   dhcp-match=set:efi,option:client-arch,7 
   dhcp-match=set:efi,option:client-arch,9 
   dhcp-match=aarch64, option:client-arch,11 
   dhcp-boot=tag:aarch64,grubaa64.efi 
   dhcp-boot=tag:!aarch64,tag:efi,grubx64.efi 
   dhcp-boot=tag:!aarch64,tag:!efi,pxelinux.0 
   
   tftp-root=/tftpboot                       #替换为实际tftpboot目录 
   log-facility=/var/log/dnsmasq.log
   ```

   6、启动服务

   ```
   $ systemctl enable --now openstack-ironic-inspector.service 
   $ systemctl enable --now openstack-ironic-inspector-dnsmasq.service
   ```

3. deploy ramdisk镜像制作

   目前ramdisk镜像支持通过ironic python agent builder来进行制作，这里介绍下使用这个工具构建ironic使用的deploy镜像的完整过程。

   ##### 安装 ironic-python-agent-builder

   1. 本地安装python3，并且将本地的python切换到python3，然后解决下切换之后的问题（如yum源无法使用的问题）：

      ```
      yum install python36
      ```

   2. 安装工具：

      ```
      pip install ironic-python-agent-builder
      ```

   3. 修改以下文件中的python解释器：

      ```
      /usr/bin/yum /usr/libexec/urlgrabber-ext-down
      ```

   4. 安装其它必须的工具：

      ```
      yum install git
      ```

      由于`DIB`依赖`semanage`命令，所以在制作镜像之前确定该命令是否可用：`semanage --help`，如果提示无此命令，安装即可：

      ```
      # 先查询需要安装哪个包
      [root@localhost ~]# yum provides /usr/sbin/semanage
      已加载插件：fastestmirror
      Loading mirror speeds from cached hostfile
       * base: mirror.vcu.edu
       * extras: mirror.vcu.edu
       * updates: mirror.math.princeton.edu
      policycoreutils-python-2.5-34.el7.aarch64 : SELinux policy core python utilities
      源    ：base
      匹配来源：
      文件名    ：/usr/sbin/semanage
      # 安装
      [root@localhost ~]# yum install policycoreutils-python
      ```

   ##### 制作镜像

   经过测试目前centos只支持8版本，而且centos8-minimal缺少部分网卡驱动，导致Dell的物理机启动之后所有的网卡都是down状态，所以我们这次使用centos8。添加如下环境变量：

   ```
   export DIB_PYTHON_VERSION=3 \ 
   export DIB_RELEASE=8 \ 
   export DIB_YUM_MINIMAL_CREATE_INTERFACES
   ```

   如果是`arm`架构，还需要添加：

   ```
   export ARCH=aarch64
   ```

   ###### 普通镜像

   基本用法：

   ```
   usage: ironic-python-agent-builder [-h] [-r RELEASE] [-o OUTPUT] [-e ELEMENT]
                                      [-b BRANCH] [-v] [--extra-args EXTRA_ARGS]
                                      distribution
    
   positional arguments:
     distribution          Distribution to use
    
   optional arguments:
     -h, --help            show this help message and exit
     -r RELEASE, --release RELEASE
                           Distribution release to use
     -o OUTPUT, --output OUTPUT
                           Output base file name
     -e ELEMENT, --element ELEMENT
                           Additional DIB element to use
     -b BRANCH, --branch BRANCH
                           If set, override the branch that is used for ironic-
                           python-agent and requirements
     -v, --verbose         Enable verbose logging in diskimage-builder
     --extra-args EXTRA_ARGS
                           Extra arguments to pass to diskimage-builder
   ```

   举例说明：

   ```
   ironic-python-agent-builder centos -o /mnt/ironic-agent-ssh -b origin/stable/rocky
   ```

   ###### 允许ssh登陆

   初始化环境变量，然后制作镜像：

   ```
   export DIB_DEV_USER_USERNAME=ipa \
   export DIB_DEV_USER_PWDLESS_SUDO=yes \
   export DIB_DEV_USER_PASSWORD='123'
   ironic-python-agent-builder centos -o /mnt/ironic-agent-ssh -b origin/stable/rocky -e selinux-permissive -e devuser
   ```

   ###### 指定代码仓库

   初始化对应的环境变量，然后制作镜像：

   ```
   # 指定仓库地址以及版本
   DIB_REPOLOCATION_ironic_python_agent=git@172.20.2.149:liuzz/ironic-python-agent.git
   DIB_REPOREF_ironic_python_agent=origin/develop
    
   # 直接从gerrit上clone代码
   DIB_REPOLOCATION_ironic_python_agent=https://review.opendev.org/openstack/ironic-python-agent
   DIB_REPOREF_ironic_python_agent=refs/changes/43/701043/1
   ```

   参考：[source-repositories](https://docs.openstack.org/diskimage-builder/latest/elements/source-repositories/README.html)。

   指定仓库地址及版本验证成功。