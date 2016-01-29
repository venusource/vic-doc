# 服务简介

## nova - 计算服务

Nova提供计算服务，是整个Openstack的核心，主要由以下组件组成：

- openstack-nova-api ： 接收用户的API调用。支持OpenStack Compute API、Amazon EC2 API和特殊用途的Admin API。
- openstack-nova-compute ： 使用hypervisor API创建和终止虚拟机。基本功能就是从消息队列获取动作并执行，更新虚拟机状态到数据库。
- openstack-nova-scheduler： 从消息队列获取虚拟机实例并确定部署到哪个nova服务主机。
- openstack-nova-conductor： nova-compute和数据库之间的交互中介，用于防止nova-compute直接访问数据库。nova-conductor可以水平扩展，但是不要和nova-compute部署在一起。
- openstack-nova-consoleauth： 根据nova-console提供的Token进行用户授权。
- openstack-nova-novncproxy： 通过浏览器访问虚拟机的vnc连接代理。
- openstack-nova-cert：管理x509证书的服务。

Nova支持多种虚拟化平台，完整列表参考[官方支持矩阵](http://docs.openstack.org/developer/nova/support-matrix.html)。另外，VIC继续维护了PowerVM的驱动，并在支持IVM基础上增加了对HMC的支持。

## glance - 镜像服务

Glance 提供Image服务，通过REST API，实现发现、注册和获取虚拟机镜像功能。镜像可以存储在多种位置，如文件系统、对象存储系统等。
如果使用文件系统作为后端(file)，镜像默认存储在`/var/lib/glance/images/`目录下。

Glance有以下组件组成：

- openstack-glance-api ： 接收镜像管理访问请求。
- openstack-glance-registry ： 处理镜像的元数据，如大小、类型等。

glance统一管理多个虚拟化平台的镜像，但是每个虚拟化平台只能使用它支持的镜像创建云主机。

## neutron - 网络服务

Neutron提供虚拟网络服务，管理网络、IP地址资源、路由器等虚拟网络资源。Neutron通过不同的插件对接各个厂商或者类型的网络设备。Neutron有以下组件组成：

- neutron-server: 提供REST API，接收网络管理访问请求。
- neutron-dhcp-agent: 管理dhcp子服务的代理。
- neutron-openvswitch-agent: 支持通过OpenvSwitch实现虚拟网络的代理。 
- neutron-l3-agent: 管理路由器子服务的代理。
- neutron-metadata-agent: 为云主机提供元数据代理服务。

Neutron支持多种方式实现不同租户之间的网络隔离，常用的有VLAN、VXLAN、GRE。

## cinder - 块存储服务

Cinder提供块存储服务，为云主机提供持久块设备。Cinder有以下组件组成：

- openstack-cinder-api: 提供REST API，接收块存储管理访问请求。
- openstack-cinder-scheduler: 调度块存储请求到合适的cinder-volume服务。
- openstack-cinder-volume: 管理块设备的服务，支持多种后端实现，对接不同的设备。
- openstack-cinder-backup: 块设备备份服务。

## keystone - 认证服务

Keystone为平台提供统一的认证服务以及内部服务目录管理。Keystone有一个服务openstack-keystone。

## horizon - 管理控制台

horizon是平台的WEB管理控制台。

## 基础组件

基础组件是平台依赖的数据库和消息中间件，主要有：

- mysql(mariadb): 存储虚拟资源配置和状态数据。
- rabbitmq: 各个服务之间通信所使用的消息中间件。
- apache2: 用于承载一些WSGI服务，如horizon。
