---
layout: articles_item
title: 用工具挖掘方块
categories: [Minecraft Forge Mod]
tags: [minecraft fore mod]
---

本节将概要的说明Vanilla怎么使用工具挖掘方块以及当方块破坏后收获物品。

### 关键概念

* 方块的硬度影响挖掘的速度。一般泥土的硬度是最低0.5，黑曜石的硬度是最高2000。有些方块刀枪不入(不可破坏的)，比如命令方块或屏障。方块的硬度一般在构造方块时使用`.setHardness()`或`.setUnbreakable()`。
* 物品可以标记一个或多个工具类，比如"axe"(斧), "pickaxe"(镐), "shovel"(铲)。Vanilla的物品最多一个工具类，但是自定义的物品(继承`ItemTool`)可以有多个工具类。可以使用`Item.setHarvesLevel(ToolClass)`设置物品的工具类。
* 方块可以被某一个特定的工具容易破坏(例如: 木头容易被斧头破坏，泥土容易被铲破坏)。当采矿时，使用的物品的工具类与方块匹配，挖掘的速度是乘以工具的“EfficiencyOnProperMaterial”。例如，用铁斧砍伐木头的速度是镐的6倍。方块的工具类使用`Block.setHarvesLevel(ToolClass)`设置。在ItemTool构造中指定Item.ToolMaterial决定了"EfficiencyOnProperMaterial"
* 方块对于不同材质(如: 木头， 石头，金，铁，钻石)的工具有不同的阻力，方块的阻力称为它的收获等级，范围是从木头的0到钻石的3。如果工具的收获等级小于方块的收获等级，采矿就非常的慢并且当方块破坏是也不能获得任何的物品。物品的收获等级使用`Item.setHarvestLevel(ToolClass, level)`设置，方块的收获等级使用`Block.setHarvestLevel(ToolClass, level)`设置。如果需要不同的IBlockState可以关联不同的收获等级。
* 

### 一般情况

### 参考文档

* [英文原版](http://greyminecraftcoder.blogspot.ch/2015/01/mining-blocks-with-tools.html)
