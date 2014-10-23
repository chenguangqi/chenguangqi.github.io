---
layout: articles_item
title: Mininet内置网络拓扑
author: chenguangqi
categories: [Tools]
---

Mininet的内置网络拓扑分别是linear、minimal、reversed、single、torus、tree等六种。
下面主要对其参数格式进行说明。

## 线性拓扑(linear)

参数格式：

```
--topo=linear,k=x,n=x
--topo=linear,k,n
```

其中k表示switch的个数，n表示每个switch连接的host的个数。

## 最小拓扑(minimal)

参数格式：

```
--topo=minimal
--topo=minimal
```

k=2时的单switch拓扑。

## 单switch反相拓扑(reversed)

参数格式：

```
--topo=reversed,k=x
--topo=reversed,k
```

其中k表示switch连接的host的个数。
host的最小编号与switch的端口的最大编号连接

## 单switch拓扑(single)

参数格式：

```
--topo=single,k=x
--topo=single,k
```

其中k表示switch连接的host的个数。

## 环形拓扑(torus)

参数格式：

```
--topo=torus,x=xxx,y=xxx
--topo=torus,x,y
```

其中x和y都必须大于等3。
由于网络中存在环路，所以需要STP功能，否则网络不能工作。

## 树形拓扑(tree)

参数格式：

```
--topo=tree,depth=x,fanout=x
--topo=tree,depth,fanout
```

其中depth表示拓扑的深度，fanout表示拓扑的广度，并且叶子节点都是host，
非叶子节点都是switch，每个非叶子节点都有fanout个子节点。
