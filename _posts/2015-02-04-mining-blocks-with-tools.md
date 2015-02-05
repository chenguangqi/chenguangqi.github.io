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
* 除了工具类，ItemTool也有一个他们影响的方块(如:可以采集的非常快)的集合。这些"EFFCTIVE_ON"的方块在ItemTool的构造中以集合的方式指定。
* 当破坏时，如果使用对应的工具就会收获方块。并随机收获特定类型的物品。如果工具附魔了精致采集，就会改变掉落物品的类型。例如采集石块就掉落石块物品而不是圆石物品。
* 采矿块的逻辑是相当复杂的，而且可以用许多不同的方式可被"截获"。请参阅下面的更多详细信息的链接。

### 一般情况

* 如果可能，给你的物品或方块适当的工具类，材质，有效方块集合来产生期望的采集速度和收获行为。或者更简单，如果你想要你的物品的行为与Vanilla物品的相同(比如ItemPickaxe)，那就继承ItemPickaxe类。
* 否则，如果你添加自定义的方块，覆写方块的对应方法。如果你添加自定义的物品，覆写物品的对应方法。如果可以，避免覆写方块和物品的高级方法；使用低级方法代替。作为指导，如果Vanilla的基本方法没有被任何Vanilla类覆写，你可以也不应该这么做，除非你有更好的理由。
* 如果你想修改Vanilla方块中Vanilla物品的行为，可以使用事件机制。

各种相关的事件，方块方法和物品方法在下面列出。那些最有可能是有用的以粗体突出显示。其他人可能会在特定情况下，但不经常用。

### 物品

**Item.setHarvestLevel(ToolClass, level)** -- 首先选择，给你的工具一个或多个工具类，如已经存在的工具"pickaxe", "axe"。

**ItemTool constructor** --

* 有效方块集合 -- 添加额外的特定方块
* ToolMaterial，影响最大挖掘速度和工具的耐久。

**Item.getStrVsBlock** -- 添加更特殊的情况，不能被ToolClass和ItemTool的构造覆盖。

**Item.getDigSpeed()** -- 

**Item.onBlockStartBreak()** -- 能用于在方块破坏前终止方块的破坏。

**Item.onBlockDestroyed()** -- 用与方块破坏时，损失物品(工具)。

在冒险模式中，在这个工具的物品堆中"CanDestroy" NBT标记决定了哪些方块能被破坏。

### 方块

首先最常见的情况:

* **Block.setHardness()** -- 设置方块的基本硬度。
* **Block.setHarvestLevel(ToolClass, level)** -- 指定这个方块有效的工具类型和是什么作成的工具以至于收获掉落的物品。这里有两个版本，一个用于普通方块，一个依赖于方块状态的方块。例如树由不同的木头组成，带有不同韧度。
* **Block.onBlockHarvested(ToolClass, level)** -- 这个方法多数情况是什么也不做，除了添加额外的效果。如：TNT产生的爆炸。
* **Block.getDrops()** -- 取得收获方块时可能掉落的物品。在简单的情况下，你可以覆写quantityDropped()和getItemDropped()方法。

一些特殊的情况:

* **Block.onBlockDestroyedByPlayer()** -- 用于掉落额外的物品(如:骨头)或多方块的一部分破坏时(如:床)
* **Block.harvestBlock()** -- 可以用于覆写默认的收获行为(如：剪树叶来增加树苗的掉落几率和触发特定的成就)。
* **Blcok.createStackedBlock()** -- 设置精准采集的掉落物品类型。
* **Block.dropBlcokAsItemWithChance()** -- 自定义随机掉落，如：忽略收获小麦是的时运附魔效果。

其他Vanilla使用的方块方法，但是从来没有被覆写。

* Block.getBlockHardness()
* Block.isToolEffective()
* Block.getHarvestTool()
* Block.getPlayerRelativeBlockHardness()
* Block.canHarvestBlock()
* Blcok.removeByPlayer()
* Block.canSilkHarvest()
* Block.dropBlockAsItem()


### 事件

* PlayerInteractEvent(LEFT\_CLICK\_BLOCK) -- 用于开始前取消挖掘。
* PlayerEvent.BreakSpeed -- 改变挖掘速度。
* PlayerEvent.HarvestCheck -- 
* BlockEvent.BreakEvent -- 
* BlockEvent.HarvestDropsEvent --



### 参考文档

* [英文原版](http://greyminecraftcoder.blogspot.ch/2015/01/mining-blocks-with-tools.html)
