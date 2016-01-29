# 基本运维任务

## 常用部署结构

一般分控制节点和计算节点两个角色。
控制节点上部署以下组件：

- mysql、rabbitmq等中间件
- keystone
- glance
- neutron
- cinder
- nova 除nova-compute之外的其他服务

计算节点上部署以下组件：

- nova-compute
- neutron-openvswitch-agent

## 配置信息和日志

服务|配置|日志
----|----|----
nova-api|/etc/nova/nova.conf|/var/log/nova/api.log
nova-cert||/var/log/nova/cert.log
nova-conductor||/var/log/nova/conductor.log
nova-consoleauth||/var/log/nova/consoleauth.log
nova-compute||/var/log/nova/compute.log
nova-scheduler||/var/log/nova/scheduler.log
glance-api|/etc/glance/glance.conf|/var/log/glance/api.log
glance-registry||/var/log/glance/registry.log
cinder-api|/etc/cinder/cinder.conf|/var/log/cinder/api.log
cinder-scheduler||/var/log/cinder/scheduler.log
cinder-volume||/var/log/cinder/volume.log
neutron-server|/etc/neutron/neutron.conf|/var/log/neutron/server.log
neutron-dhcp-agent|/etc/neutron/dhcp_agent.ini|/var/log/neutron/dhcp_agent.log
neutron-l3-agent|/etc/neutron/l3_agent.ini|/var/log/neutron/l3_agent.log
neutron-openvswitch-agent|/etc/neutron/plugin.ini|/var/log/neutron/openvswitch-agent.log
keystone|/etc/keystone/keystone.conf|/var/log/keystone/keystone.log

## 服务管理

### 查看服务状态

```
# cd ~
# source openrc
# openstack-status 
== Nova services ==
openstack-nova-api:                     active
openstack-nova-cert:                    active
openstack-nova-compute:                 active    (disabled on boot)
openstack-nova-network:                 dead      (disabled on boot)
openstack-nova-scheduler:               active
openstack-nova-conductor:               active
== Glance services ==
openstack-glance-api:                   active
openstack-glance-registry:              active
== Keystone service ==
openstack-keystone:                     active
== Horizon service ==
openstack-dashboard:                    404
== neutron services ==
neutron-server:                         active
neutron-dhcp-agent:                     active
neutron-l3-agent:                       active
neutron-metadata-agent:                 active
neutron-lbaas-agent:                    active
neutron-openvswitch-agent:              active
== Cinder services ==
openstack-cinder-api:                   active
openstack-cinder-scheduler:             active
openstack-cinder-volume:                active
openstack-cinder-backup:                inactive  (disabled on boot)
== Ceilometer services ==
openstack-ceilometer-api:               active
openstack-ceilometer-central:           active
openstack-ceilometer-compute:           inactive  (disabled on boot)
openstack-ceilometer-collector:         active
openstack-ceilometer-alarm-notifier:    active
openstack-ceilometer-alarm-evaluator:   active
== Heat services ==
openstack-heat-api:                     active
openstack-heat-api-cfn:                 active
openstack-heat-api-cloudwatch:          dead      (disabled on boot)
openstack-heat-engine:                  active
== Support services ==
libvirtd:                               active
openvswitch:                            active
messagebus:                             active
tgtd:                                   active
rabbitmq-server:                        active
memcached:                              active
== Keystone users ==
Warning keystonerc not sourced
```

### 控制服务

`openstack-service {status|start|stop|restart} {nova|glance|keystone|cinder|neutron}`

### KVM云主机存储结构

KVM云主机默认会在计算节点的/var/lib/nova/instances目录下存储配置信息和虚拟磁盘文件，目录结构如下：

```
# tree -l 1 /var/lib/nova/instances
/var/lib/nova/instances/
├── 35fc0c02-9708-45d6-a2e1-d03863b158cf
│   ├── console.log
│   ├── disk
│   ├── disk.config
│   ├── disk.info
│   └── libvirt.xml
├── _base
│   ├── 34b8f4e25cb748adccff259df0df882fd230ad7e
├── compute_nodes
├── locks
│   ├── nova-34b8f4e25cb748adccff259df0df882fd230ad7e
│   └── nova-storage-registry-lock
└── snapshots
```

其中:

- `35fc0c02-9708-45d6-a2e1-d03863b158cf`是云主机的uuid，这个目录下的disk是云主机的root磁盘，libvirt.xml是云主机的libvirt配置文件，console.log是启动日志。
- _base目录是镜像缓存目录，`34b8f4e25cb748adccff259df0df882fd230ad7e`是缓存的一个镜像。
