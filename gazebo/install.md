---
layout: default
title: 安装
---


## 下载并安装Gazebo

### Ubuntu

#### 分步安装

* 设置你的计算机可以接收来自packages.osrfoundation.org的软件。

**注意:**这是该仓库的[有效镜像](https://bitbucket.org/osrf/gazebo/wiki/gazebo_mirrors)列表，可以提高下载速度。

```bash
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-latest.list'
```

* 设置key

```bash
wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
```

* 安装Gazebo

```bash
sudo apt-get update
sudo apt-get install gazebo4
# For developers that works on top of Gazebo, one extra package
sudo apt-get install libgazebo4-dev
```

* 检测安装

    **注意:**第一次执行`gazebo`需要下载一些必须的模型，会花费一些时间，请耐心等待。

```bash
gazebo
```
