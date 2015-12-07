## binding.py

对容器的接口进行本地绑定相关的操作函数，实际干活的。

### port_bind
```python
port_bind(endpoint_id, neutron_port, neutron_subnets)
```

将给定的 neutron 端口绑定到本地的一个容器上。

主要使用pyroute2 包来实现 veth 端口对的连接。

### port_unbind