# FAQ

## 启动云主机时，管理控制台提示“No valid host was found”

该问题是因为调度器无法找到合适的主机来创建用户请求的云主机，但是原因可能是多方面的，比如资源不足、镜像与虚拟化平台不匹配等。具体的原因目前只能根据日志判断，主要相关日志是`/var/log/nova/scheduler.log`，在这个日志中从后往前查找`ERROR`，可以定位到具体原因附近的日志。

## 从管理控制台无法连接到云主机

一般情况下horizon使用noVNC代理访问云主机，因此可以检查以下几个环节。

- 控制节点上openstack-nova-novncproxy是否启动
- 客户端是否能正常访问到计算节点上nova.conf中配置项novncproxy_base_url指定的主机地址，尤其是使用域名时。
- 计算节点的防火墙是否开启5900:5964端口
- 如果从非管理网段访问VNC，检查控制节点上nova.conf中配置项novncproxy_host是否为0.0.0.0，以便接收所有网段的连接
- 如果是使用vmware虚拟化，检查esxi服务器是否已经开启vnc服务

使用以下命令临时打开VNC所需要的端口范围，iptables服务重启后失效。

```
# iptables -I INPUT 1 -p tcp -m multiport --ports 5900:5964 -m comment --comment "Enable VNC connections" -j ACCEPT
```

通过编辑`/etc/sysconfig/iptables`，在合适位置增加以下规则实现永久修改。

```
-A INPUT -p tcp -m multiport --ports 5900:5964 -m comment --comment "Enable VNC connections" -j ACCEPT
```

注意：修改nova.conf配置后要重启nova服务。
