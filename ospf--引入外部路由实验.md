



# ospf--引入外部路由实验



![1587801977333](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1587801977333.png)

实验要求：

创建ospf1 、ospf2，并根据拓扑图划分区域的地址，r1上引入type-1的外部路由



```
[r1]display     cu  conf   ospf  
#
ospf 1 router-id 1.1.1.1
 import-route ospf 2 type 1
 area 0.0.0.0
  network 10.1.1.0 0.0.0.3
  network 30.1.1.0 0.0.0.3
  network 1.1.1.1 0.0.0.0
#
ospf 2 router-id 1.1.1.1
 import-route ospf 1 type 1
 area 0.0.0.0
  network 40.1.1.0 0.0.0.3
#


```

```
[r2]dis  current-configuration   conf   ospf 
#
ospf 1 router-id 2.2.2.2
 area 0.0.0.0
  network 20.1.1.0 0.0.0.3
  network 10.1.1.0 0.0.0.3
  network 2.2.2.2 0.0.0.0
 area 0.0.0.1
  network 50.1.1.0 0.0.0.3
#

```

```
[r3]dis  cu  conf   ospf 
#
ospf 1 router-id 3.3.3.3
 area 0.0.0.0
  network 30.1.1.0 0.0.0.3
  network 20.1.1.0 0.0.0.3
  network 3.3.3.3 0.0.0.0
 area 0.0.0.2
  network 60.1.1.0 0.0.0.3
#

```

```
[r4]dis   cu  conf   os
#
ospf 2 router-id 4.4.4.4
 area 0.0.0.0
  network 40.1.1.0 0.0.0.3
#

```

```
[r5]dis  cu   conf  os
#
ospf 1 router-id 5.5.5.5
 area 0.0.0.1
  network 50.1.1.0 0.0.0.3
#

```

```
[r6]dis  cu  conf   os
#
ospf 1 router-id 6.6.6.6
 area 0.0.0.2
  network 60.1.1.0 0.0.0.3
#

```



**验证**

```
#
dis   ospf   routing
dis  ip  route-table
dis  ospf  lsdb   ase  self-originate 
#
```





















