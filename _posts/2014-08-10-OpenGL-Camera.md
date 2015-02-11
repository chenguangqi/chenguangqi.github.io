---
layout: articles_item
title: "OpenGL和相机"
author: chenguangqi
categories: [OpenGL]
---

描述OpenGL的坐标系和相机。

### 坐标系
有些定位$(X, Y, Z)$轴的方法:

* Z轴正方向上：在Blender中使用，这意味着$(X, Y)$是地面，Z是高度。
* Y轴正方向上：在OpenGL中使用，这意味着$(X, Y)$是墙，这样Y是高度，Z是离墙的远近。
* 右手规则/左手规则：OpenGL和Blender使用右手规则坐标系，这意味着可以用右手表示它，拇指代表X，食指代表Y，中指代表Z。左手坐标系，如DirectX使用，可以用左手以相似的方法表示。

 ![](http://upload.wikimedia.org/wikipedia/commons/7/79/Rechte-hand-regel.jpg)

在OpenGL中使用Blender的Z轴向上的坐标系是很吸引人的，但有一些问题:

* .obj文件是Y轴向上的，当加载.obj文件时(或者当从Blender中导出.obj文件时)需要修改。
* 相机是Y轴向上的，所以如果你使用Z轴向上的坐标系，而默认相机是在$(0, 0, 0)$，那它的脸就会向下。当旋转相机的时候就会混乱，因为如果你的相机镜头水平向着Y轴，当你绕Z轴旋转是向左右看，
你实际旋转视图是绕Y中的。
* 其他引擎，如Irrlicht和Ogre，也使用Y轴向上的右手坐标系。

因此，我们坚持使用Y轴向上的右手坐标系。

### 定位相机
在OpenGL中，没有内置相机的概念。我们必须以欺骗的方式实现它。
下面是我们的技术：我们不考虑在一个固定的世界如何移动相机, 而是围绕着我们的相机如何移动整个世界。例如，沿着X轴将相机移动3个单位与沿着X轴将整个世界移动-3个单位是相同的。其他轴的移动和旋转也是这样的。使用相机时要牢记这一点，最终结果会感到很直观，但是每当你使用相机的时候，
如果你忘记我们使用这个欺骗的技术，那么就会得到错误的变换。

### 控制相机
我们首先从一个直观的实现开始:
* 按左右键，相机绕Y轴旋转
* 按上下键，相机前后移动
* 按上下翻页键，相机绕X轴旋转

向前向后移动，我们使用`glm::translate`。它有两种格式：
* `glm::translate(transformation_matrix, glm::vec3(dx, dy, dz))`
* `glm::translate(glm::mat4(1), glm::vec3(dx, dy, dx)) * transformation_matrix`

他们都是做`transformation_matrix`变换，然后做$(dx, dy, dz)$的移动。

* 在变换相机时，不要忘了我们变换的是世界，而不是相机。因此上面的世界到相机的变换矩阵是相反的。

例如，如果`transformation_matrix`是相机向右旋转$90^\circ$，然后沿着Z轴移动。
* 







### 参考文档

* [Modern_OpenGL_Tutorial_Navigation](http://en.wikibooks.org/wiki/OpenGL_Programming/Modern_OpenGL_Tutorial_Navigation)
