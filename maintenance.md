# 日常维护

平台的服务完成的是虚拟资源的管理功能，它们即使发生故障会影响对虚拟资源的管理操作，而对虚拟资源上运行的业务没有影响。

## 控制节点

### 计划内维护

建议在没有业务的时间执行，例如凌晨1-2点。

### 重启

可以使用操作系统的`reboot`命令进行重启，或者使用`openstack-service`命令执行个别服务的重启。
重启完成以后，需要确认以下服务是否启动成功。

```
== Nova services ==
openstack-nova-api:                     active
openstack-nova-cert:                    active
openstack-nova-compute:                 active
openstack-nova-scheduler:               active
openstack-nova-conductor:               active
== Glance services ==
openstack-glance-api:                   active
openstack-glance-registry:              active
== Keystone service ==
openstack-keystone:                     active
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
== Ceilometer services ==
openstack-ceilometer-api:               active
openstack-ceilometer-central:           active
openstack-ceilometer-collector:         active
openstack-ceilometer-alarm-notifier:    active
openstack-ceilometer-alarm-evaluator:   active
== Support services ==
openvswitch:                            active
messagebus:                             active
rabbitmq-server:                        active
memcached:                              active
```

## 计算节点

### 计划内维护

如果需要重启计算节点，首先先把该节点上的所有云主机迁移到其他节点。

查找需要移动的所有云主机

```
# source ~/openrc
# nova list --host c01.example.com --all-tenants
```

将这些云主机逐个迁移到其他节点

`# nova live-migration <uuid> c02.example.com`

如果没有使用共享存储保存云主机的root磁盘，需要指定`--block-migrate`选项，将云主机存储一起迁移。

停止计算服务，并进行节点维护

`# service stop openstack-nova-compute`

重启后检查服务状态

完成计算节点重启后，检查计算服务是否正常启动。

`# service status openstack-nova-compute`

还需要检查计算服务是否连接上了消息中间件。注意下面输出的时间是执行重启操作的时间。


```
# grep AMQP /var/log/nova/compute.log | tail -n 1
2016-01-29 10:00:34.777 18375 INFO oslo.messaging._drivers.impl_rabbit [-] Connected to AMQP server on 10.68.19.8:5672
```
