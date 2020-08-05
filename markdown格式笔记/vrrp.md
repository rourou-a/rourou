# vrrp

- 虚拟路由器冗余协议（网关）


## 选举规则

- 根据优先级，选举出master，承担网关功能

| 角色   | 备注                                                  |
| ------ | ----------------------------------------------------- |
| master | 负责对虚拟ip地址的`arp请求`，转发发往虚拟网关的数据包 |
| backup | 负责在master故障后，`接替`master的工作                |

**优先级**

- 0-255，默认100，`越大越优先`
- 255：保留给`ip地址拥有者`使用
- 0：用于触发backup立即成为master（如：主动退出vrrp组）
- 如果优先级一致，`ip地址越大越优先`

**抢占规则**

- 默认开启抢占

**工作流程**



- vrrp报文比较，优先级高保持backup
- 

- 辨别网关是否真假，使用`trace`查看
- 故障切换，收不到master报文就切换master

## 定时器

| 定时器         | 备注                                                         |
| -------------- | ------------------------------------------------------------ |
| 通告间隔定时器 | `默认1s`。master会定时大宋vrrp通告报文，通告自己正常工作。如果backup在等待`3个间隔时间`后，依旧没有收到通告报文，重新进行master的选举 |
| 抢占延迟定时器 | `默认0s`，为了避免频繁进行`主备`转换，让backup有足够的时间搜索必要的信息（如路由信息）。backup接受到优先级较低的vrrp通告报文后，才会对发送vrrp通告报文取代原来的master |

- 只有master发包

**vrrp报文**

- 协议号`112`,组播地址`224.0.0.18`
- 一个接口多个组，一个组多个地址

**vrrp状态机制**

- initialize
- master
- backup

查看

```
#
dis   vrrp   int     vlanif  x  //查看接口下的信息
#
```

接口地址拥有者

- 优先级自动调成255

```
vrrp   vrid  1   pri   xxx  修改优先级
```

**vrrp设计注意事项**

- master应该和stp桥根保持一致，否则导致次优路径

**应用**

- 主备备份
- 负载均衡

## vrrp跟踪

- 当master上行链路故障时，自动修改优先级，重新选举master（自废武功）
- 优先级自动减10,有可能减的数字不够，查看后再减（排错）
- 接口复活后悔自动恢复

**配置命令**

| 命令                                                  | 备注                   |
| ----------------------------------------------------- | ---------------------- |
| vrrp  vrid  virtual-ip  xxxx                          | 创建备份组及虚拟ip地址 |
| vrrp  vrid  1  priority  100                          | 配置优先级             |
| vrrp  vrid  1  timer advertise 1                      | 配置通告延迟           |
| vrrp  vrid  1   preempt-mode  disable                 | 关闭抢占通道           |
| vrrp  vrid 1  preempt-mode  timer delay 3             | 配置抢占延迟           |
| vrrp  vrid 1  authentication-mode  simple/md5  rourou | 配置认证               |
| vrrp   vrid  1   track  interface  vlanif  2          | 配置接口跟踪           |
| display  vrrp   birf/interface                        | 验证vrrp               |

