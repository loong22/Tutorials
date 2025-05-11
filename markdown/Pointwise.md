
软件学习笔记
------

Pointwise网格生成流程
---------------

```
1 打开/创建 new
2 选择求解软件，2D/3D
3 模型导入或创建
4 创建 curve/connector （几何清理或缝合，设置默认布点及点分布，合理使用层管理功能）
5 指定 connector 的网格点
6 创建 domains （选择一个算法，提高面网格质量）
7 创建 blocks （选择一个算法，提高体网格质量）
8 导出数据文件 （设置求解器的边界条件、流体固体域）
9 保存 project
```

结构性网格生成条件
---------

```
结构网格domains必须是四边形或者拓扑四边形（0-H、H、T型网格等）， 且对边的点的个数必须一致，才能生成结构网格。
```

快捷调整线上点分布的操作
------------

```
Grid- distribute-  function-copy from，选择对边的connectors
调整对边的结构性网格、保持正交性。 然后点击initialize初始化。
```

边界层的增长率生成
---------

```
Grid- distribute-  growth distribution parameters，选择边界层数量和增长率以及从哪个位置开始增长。
```

在实物面上面对生产的线、面进行project投影，确保线、面上的点都在实物表面上，以确保模型网格的准确性。
-----------------------------------------------------

```
1-直线适应Datebase
选中connector，edit，project，project，OK
2-生成的网格在两个database上面，无法自动投影为同一个实体面，选择生成的面网格，点击project，简单问题直接点project即可。 对于复杂的问题，选择target database selection， 点击需要投影的网格，begin -end -project。
```

官方教程
----

```
An Introduction to Algebraic Extrusions（代数挤压导论）

<https://www.youtube.com/watch?v=bZulldhDyh4&t=3s>
```

Corner factor（角因子） 最大值10 Features（特征） 打开时边界层增长，在法线方向上平滑增长。关闭时，增长较差。 interations （互动） 迭代次数 blend exponet（混合指数）

How to Create a Collar Grid（如何创建领子网格，ps在两个实体连接处一起增长边界层） [https://www.youtube.com/watch?v=6GTemO020ng&t=1s](https://www.youtube.com/watch?v=6GTemO020ng&t=1s)

```
按住shift，再用左键拖动，可以选择有连接闭合的connectors（线）而其他没有闭合的线不会被选中
面交界处的线网格上面的点分布要一致或者差不多，来保证网格的连续性。通过调整connector spacing
```

area ratio（面积比） 小于4

implicit （隐式的） 值要是 explicit（显式的）两倍

Normal Extrusions - YouTube [https://www.youtube.com/watch?v=Qh2u\_ojPFDM&t=25s](https://www.youtube.com/watch?v=Qh2u_ojPFDM&t=25s)

Splay（张开） 参数在0.1-1之间，越大，张开度越大

The flow chart
--------------

建立控制方程 确定初始条件及边界条件 划分计算网格 生成计算节点 确定离散方程 离散初始条件和边界条件 给定求解控制参数 求解离散方程 解是否收敛 显示和输出计算结果