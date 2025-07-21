# 介绍了所有基本算法流水线节点的功能

## `param_model` 插件算法
### Outline
- [Add](#Add)
- [AppLaunch](#AppLaunch)
- [Arc](#Arc)
- [Array](#Array)
- [BoundingBox](#BoundingBox)
- [Box](#Box)
- [Brep](#Brep)
- [BSPLine](#BSPLine)
- [BSPLineSurf](#BSPLineSurf)
- [Chamfer](#Chamfer)
- [CircleCenter](#CircleCenter)
- [Circle](#Circle)
- [CirclePolygon](#CirclePolygon)
- [Common](#Common)
- [Compound](#Compound)
- [Cone](#Cone)
- [Copy](#Copy)
- [Cut](#Cut)
- [Cylinder](#Cylinder)
- [Decompose](#Decompose)
- [DomainObjFile](#DomainObjFile)
- [Dxf2d](#Dxf2d)
- [Edge](#Edge)
- [EdgeArray](#EdgeArray)
- [Ellipse](#Ellipse)
- [Ellipsoid](#Ellipsoid)
- [Evolved](#Evolved)
- [Face](#Face)
- [Fillet2D](#Fillet2D)
- [Fillet](#Fillet)
- [Fuse](#Fuse)
- [GlobalParamBatch](#GlobalParamBatch)
- [Group](#Group)
- [Helix](#Helix)
- [Hyperbola](#Hyperbola)
- [IFC](#IFC)
- [Iges](#Iges)
- [IncrementalMesh](#IncrementalMesh)
- [Mirror](#Mirror)
- [Offset](#Offset)
- [OBoundingBox](#OBoundingBox)
- [Parabola](#Parabola)
- [Pipe](#Pipe)
- [PipeShell](#PipeShell)
- [Prism](#Prism)
- [ProjectCurve](#ProjectCurve)
- [ProjectSurf](#ProjectSurf)
- [PyScript](#PyScript)
- [Remove](#Remove)
- [ResultFilter](#ResultFilter)
- [Revol](#Revol)
- [RingArray](#RingArray)
- [Rotate](#Rotate)
- [Scale](#Scale)
- [Script](#Script)
- [Section](#Section)
- [Shell](#Shell)
- [Solid](#Solid)
- [Sphere](#Sphere)
- [SpherialCrown](#SpherialCrown)
- [Split](#Split)
- [SplitByPlane](#SplitByPlane)
- [SplitEdge](#SplitEdge)
- [Step](#Step)
- [Stl](#Stl)
- [STLWriter](#STLWriter)
- [SVGWriter](#SVGWriter)
- [Tetrahedron](#Tetrahedron)
- [ThickSolid](#ThickSolid)
- [Thrusections](#Thrusections)
- [ThrusectionsPath](#ThrusectionsPath)
- [Torus](#Torus)
- [Transform](#Transform)
- [TransformRT](#TransformRT)
- [TransformRTAS](#TransformRTAS)
- [Translation](#Translation)
- [Vector](#Vector)
- [Vertex](#Vertex)
- [Wedge](#Wedge)
- [Wire](#Wire)

**************************************************************
[返回目录](#Outline)
### add
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
一个`OCC Shape`形成的列表


- 输出结果:  
一个`OCC Shape`对象


- 功能:  
```
向shapes_list中第一个shape添加其余shape,即将一个Shape添加到另外一个Shape中
Add函数通过一个静态的检查列表，来检查添加的Shape是不是合法的，
即FACE只能添加到SHELL和COMPOUND中，EDGE只能添加到WIRE，SOLID和COMPOUND中等。
添加之后还检查了Shape的ORIENTATION及位置信息并作相应调整。不满足条件的情况都会抛出异常，
所以对于Add函数需要增加异常处理逻辑。
使用这个函数需要注意的是这个Add只是简单的将Shape添加到TShape的Shape表中，并没有维护BREP的边界信息。
```
**************************************************************
[返回目录](#Outline)
### AppLaunch
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "app_launch": True, # 是否执行启动行为
    "app_name": "echo", # 需要启动的app名称
    "app_args": [], # 启动参数列表
    "work_dir": None, # 启动子进程的工作目录
    "use_work_dir": False, # 是否使用子进程工作目录, 如果为false则work_dir参数无效
    "use_cmd_str":False, # 是否给定命令字符串,false则cmd_str参数无效, true则 app_name和app_args参数无效
    "cmd_str": "" # 完整的命令字符串
}
```

- 输入图元:  
[]


- 输出结果:  
[]


- 功能:  
```
在子进程中执行命令行，主要用于启动一个第三方应用程序
```
**************************************************************
[返回目录](#Outline)
### Arc
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0],[1,0,0],[1,1,0]]
parameters = {
    "r": 1.0, # 半径
    "alpha1":0.0, # 角度1
    "alpha2":1.0, # 角度2
    "circle_mode":"0p", # 圆模式
    "arc_mode":"3p", # 圆弧模式
    "sense":True # 圆弧生成方向
}
```
- 输入图元:  
[]


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
创建一个圆弧
参考图元对象包含若干点，图元组合方式与circle_mode的关系如下
0p表示0个点：圆心在原点，圆所在法向量为z轴
1p表示1个点：圆心在第1个点，圆所在法向量为z轴
2p表示2个点: 圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向
3p表示3个点：圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向，心坐标系x轴为第1个点指向第三个点的方向

当arc_mode为c或cp或c2p的时候，将创建一个gp_circle，其创建过程中会使用若干点，使用的点数根据circle_mode确定
使用过的点在后面创建arc的时候就不再重复使用了

arc_mode的说明如下:
“c”: 创建gp_cricle,并使用alpha1和alpla2参数
"cp": 创建gp_cricle,并使用alpha1和shapes_list列表中剩余点中的1个点
"c2p":创建gp_cricle,并使用alpha1和shapes_list列表中剩余点中的2个点
"3p": 使用shapes_list列表中剩余点中的3个点创建
"pvp": 使用shapes_list列表中剩余点中的3个点或4个点创建,如果是3个点，则第3个点为vector,如果是4个点,则使用第4个点vector
减去第3个点的vector形成输入vector

通过不同的模式搭配,传入的最少点数如下
"c"+"0p" : 0个点
"c"+"1p" : 1个点
"c"+"2p" : 2个点
"c"+"3p" : 3个点
"cp"+"0p" : 1个点
"cp"+"1p" : 2个点
"cp"+"2p" : 3个点
"cp"+"3p" : 4个点
"c2p"+"0p" : 2个点
"c2p"+"1p" : 3个点
"c2p"+"2p" : 4个点
"c2p"+"3p" : 5个点
"3p" :  3个点
"pvp" :  3个点

当 sense = True（默认值）：圆弧沿逆时针方向（CCW）生成。
当 sense = False：圆弧沿顺时针方向（CW）生成。
```
**************************************************************
[返回目录](#Outline)
### Array
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,10]]
parameters = {
    "step1": 1.0, # 间距1
    "step2": 1.0, # 间距2
    "step3": 1.0, # 间距3
    "number1":2, # 阵列数量1
    "number2":2, # 阵列数量2
    "number3":2, # 阵列数量3
    "combin": True # 阵列结果是否组合成1个图元
}
```
- 输入图元:  
一个`OCC Shape`形成的列表，列表中是需要阵列的对象


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
将shapes_list中的对象进行阵列，
coordinates中的第一个点是基点
coordinates中包含的其余点数决定了阵列的模式
包含：
1个点：从基点到该点进行线性阵列，阵列间距由step1决定
2个点: 从基点到点1和点2进行平面矩形阵列，间距由step1和step2决定
3个点：从基点到点1和点2及点3进行空间矩形阵列，间距由step1和step2,和step3决定
```
**************************************************************
[返回目录](#Outline)
### BoundingBox
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "tol":0.000001, # 包围盒创建的容差大小
    "use_mesh":False, # 使用网格开关
    "create_orthogonal_planes":False, # 是否创建正交对称辅助平面
    "orthogonal_planes_out_size":0.1, # 正交对称辅助平面超出包围盒的尺寸大小
}
```
- 输入图元:  
一个`OCC Shape`形成的列表，列表中是需要创建包围盒的对象


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
创建包围盒
use_mesh参数的含义是
a flag that tells whether or not the shape has first to be meshed before the bbox
computation. This produces more accurate results
```
**************************************************************
[返回目录](#Outline)
### Box
- 类型: `Creator`


- 参数:  
```python
coordinates=[[0,0,0]]
parameters = {
    "x_size": 10, # 长度(x)
    "y_size": 20, # 宽度(y)
    "z_size": 30 # 高度(z)
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建矩形块
参考图元列表,输入0到3个坐标值,具体含义见功能介绍
如果列表中不包含点，则在原点处创建
如果列表包含1个点，则在该点处创建
如果列表包含2个点，则对角线创建
如果列表包含3个点，则根据三个点所创建的坐标系统来创建
利用gp_Ax2构建一个坐标系，其中gp_Ax2(p,n,vx),p为原点，n为xy平面的法向量，vx为x轴向量
vec0 = gp_Vec(point_list[0].XYZ())
vec1 = gp_Vec(point_list[1].XYZ())
vec2 = gp_Vec(point_list[2].XYZ())
vec_0_1 = vec1 - vec0 #xoy平面的法向量
vec_0_2 = vec2 - vec0 #x轴方向向量
n_dir = gp_Dir(vec_0_1)
vx_dir = gp_Dir(vec_0_2)
ax2 = gp_Ax2(point_list[0], n_dir,vx_dir)
```
**************************************************************
[返回目录](#Outline)
### Brep
- 类型: `Loader`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
读取brep格式文件
```
**************************************************************
[返回目录](#Outline)
### BSPLine
- 类型: `Creator`


- 参数:  
```python
coordinates=[[0,0,0],[0,0,1],[0,1,1],[0,1,0]]
parameters = {}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
输入多个点坐标对象，构建B样条曲线
```
**************************************************************
[返回目录](#Outline)
### BSPLineSurf
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元,必须是bspline创建的edge对象


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
输入多个样条曲线，创建样条曲面
```
**************************************************************
[返回目录](#Outline)
### Chamfer
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "d1" : 1, # 倒角第1边长
    "d2" : 1, # 倒角第2边长
    "only_d1": True # 是否只使用第1个边长
}
```
- 输入图元:  
一个`OCC Shape`图元，即为需要倒角的对象


- 输出结果:  
一个`OCC Shape`图元，倒角结果的对象


- 功能:  
```
倒角操作
辅助的shapes_list中的第一个对象为需要操作的实体
only_dis1为True的模式下：
需要传入需要倒角的对象和一系列edge,如[实体，edge,edge,...]
only_dis1为False的模式下：
表示需要使用两个倒角距离，倒角的参数由两个距离d1和d2给出；边和面必须成对出现，
如 [实体，face,edge,face,edge,...]
即一个面后面跟着一个边，倒角的时候，给定的面用来标记，d1的那一侧。
```
**************************************************************
[返回目录](#Outline)
### CircleCenter
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
一个`OCC Shape`图元，必须是一个圆,edge类型的对象


- 输出结果:  
一个`OCC Shape`图元,是一个点对象,vertex类型


- 功能:  
```
求出给定圆的圆心
```
**************************************************************
[返回目录](#Outline)
### Circle
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r": 1.0, # 圆半径
    "wire_type": True # 是否返回wire类型的图元
}
```
- 输入图元:  
[]


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
创建一个圆
参考图元列表包含若干点
0个点：圆心在原点，圆所在法向量为z轴
1个点：圆心在第1个点，圆所在法向量为z轴
2个点: 圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向
3个点：圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向，心坐标系x轴为第1个点指向第三个点的方向
```
**************************************************************
[返回目录](#Outline)
### CirclePolygon
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "num_sides": 6, #多边形边数
    "radius": 20.0, # 多边形外接圆半径
    "side_length": None # 对变形边长，只有radius为None时，边长才生效
}
```
- 输入图元:  
[]


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
创建一个圆的内接正多边形
参考图元包含若干点，用来表达外接圆
0个点：圆心在原点，圆所在法向量为z轴
1个点：圆心在第1个点，圆所在法向量为z轴
2个点: 圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向
3个点：圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向，心坐标系x轴为第1个点指向第三个点的方向
```
**************************************************************
[返回目录](#Outline)
### Common
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
2个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
求两个图元的交集
```
**************************************************************
[返回目录](#Outline)
### Compound
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
将多个OCC图元进行组合，形成OCC的组合图元
```
**************************************************************
[返回目录](#Outline)
### Cone
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r1": 2, # 底面半径
    "r2": 1, # 上底面半径，为0则代表是圆锥，否则是圆台
    "h": 1, # 圆台高度
    "angle": None # 圆台角度，单位为弧度，2pi表示完整圆台
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建圆台(圆锥)
参考点列表规则如下
如果参考点中不包含点，则在原点处创建
如果参考点包含1个点，则在该点处创建
如果参考点包含2个点，则以该点为轴创建
如果参考点包含3个点，则根据三个点所创建的坐标系统来创建
```
**************************************************************
[返回目录](#Outline)
### Copy
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0],[1,1,1]]
parameters = {
    "combin": True # 是否将拷贝的对象组合返回
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
将给定的图元进行复制，复制到目标点
参考图元为若干点，其中的第一个点是基点，其余为目标点
```
**************************************************************
[返回目录](#Outline)
### Cut
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
布尔减法
第1个图元减去第2个图元,得到的结果再减去第3个图元,以此类推
```
**************************************************************
[返回目录](#Outline)
### Cylinder
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "radius": 10, # 半径
    "height": 20, # 高度
    "angle":None, # 圆柱旋转角度,单位为弧度, None表示整个圆柱
    "reverse":False, # 圆柱反向
}
```
- 输入图元:  
[]


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
创建一个圆柱
参考图元对象包含若干点
如果参考图元中不包含点，则在原点处创建
如果参考图元包含1个点，则在该点处创建
如果参考图元包含2个点，则以该点为轴创建
如果参考图元包含3个点，则根据三个点所创建的坐标系统来创建
利用gp_Ax2构建一个坐标系，其中gp_Ax2(p,n,vx),p为原点，n为xy平面的法向量，vx为x轴向量
vec0 = gp_Vec(point_list[0].XYZ())
vec1 = gp_Vec(point_list[1].XYZ())
vec2 = gp_Vec(point_list[2].XYZ())
vec_0_1 = vec1 - vec0  # xoy平面的法向量
vec_0_2 = vec2 - vec0  # x轴方向向量
n_dir = gp_Dir(vec_0_1)
vx_dir = gp_Dir(vec_0_2)
ax2 = gp_Ax2(point_list[0], n_dir, vx_dir)
```
**************************************************************
[返回目录](#Outline)
### Decompose
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "type":None , # 类型索引,一般设为None即可,采用type_name参数代替
    "type_name":"solid", # 分解图元的类型，可选字段为["solid","shell","face","wire","edge","vertex","shape","compound","compsolid"]
    "use_first_shape_type":False, # 使用给定的第一个图元的对象类型作为分解的对象类型,True表示第一个图元对象的目的只是确定类型,而不参与分解
    "return_index_list":[] # 通过一个索引列表决定对象分解以后那些shape返回，那些不返回，空列表表示都返回
}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
多个`OCC Shape`图元


- 功能:  
```
将一个对象按照类别分解成若干子对象返回
当type为None的时候type_name才有作用，type_name可以输入，二者是一一对应的
"solid","shell","face","wire","edge","vertex","shape","compound","compsolid":
type的类型包括
TopAbs_SOLID,TopAbs_SHELL,TopAbs_FACE,TopAbs_WIRE,TopAbs_EDGE,TopAbs_VERTEX,TopAbs_SHAPE,TopAbs_COMPOUND
return_index_list如果给定如下内容[0,2,5]
表示将给定的所有图元按照顺序进行分解,返回第0,2,5个子图元
```
**************************************************************
[返回目录](#Outline)
### DomainObjFile
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "domain_id": None, # DomainID 字符串
    "domain_type":None, # DomainType 字符串
    "io_name":None, # io接口名称,主要针对一个DomainObj对应多个io接口的情况,如果为None则返回第一个io接口
}
```
- 输入图元:  
[]


- 输出结果:  
1个任意类型的DomainObj对象


- 功能:  
```
通用加载器,主要针对持久化保存的DomainObj对象文件的加载
加载器的选择由domain_id,domain_type,io_name这3个参数组合确定
```
**************************************************************
[返回目录](#Outline)
### Dxf2d
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "dxf_loader_type": "ezdxf", # 使用哪个库加载dxf,一般使用ezdxf或gis_plugin两个选项
}
```
- 输入图元:  
[]


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
读取dxf文件的读取器
```
**************************************************************
[返回目录](#Outline)
### Edge
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,1]]
parameters = {
    "pline": True, # True表示给定的多个坐标点是一条多段线的各个端点,False表示给定的多个坐标点是一对一对出现的线段的端点
    "combin": False # 结果是否组成成组合对象返回
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
生成线段或多段线(OCC Edge对象)
给定多个坐标,pline为True表示给定的多个坐标点是一条多段线的各个端点,为False表示给定的多个坐标点是一对一对出现的线段的端点
根据combin,返回一个或多个对象
```
**************************************************************
[返回目录](#Outline)
### EdgeArray
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "number":2, # 阵列数量
    "combin": True # 阵列结果是否组合后返回
}
```
- 输入图元:  
输入多个`OCC Shape`图元,至少2个图元,其中第一个Edge类型图元为阵列路径


- 输出结果:  
一个`OCC Shape`图元


- 功能:  
```
验证线段阵列对象
参考图元的第1个坐标为阵列基点,如果没有坐标,则表示以原点为基点
输入图元中的第一个edge对象为阵列路径
```
**************************************************************
[返回目录](#Outline)
### Ellipse
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "major_r": 2.0, # 长轴半径
    "minor_r": 1.0, # 短轴半径
    "wire_type": True # 是否返回wire类型的图元
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建一个椭圆
参考图元对象包含若干点
0个点：圆心在原点，圆所在法向量为z轴
1个点：圆心在第1个点，圆所在法向量为z轴
2个点: 圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向
3个点：圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向，心坐标系x轴为第1个点指向第三个点的方向
```
**************************************************************
[返回目录](#Outline)
### Ellipsoid
- 类型: `Creator`


- 参数:  
```python
parameters = {
    "major_r": 2.0, # 长轴半径
    "minor_r": 1.0, # 短轴半径
    "major_as_axis": True # True表示绕长轴旋转形成椭球，否则为绕短轴旋转
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
在坐标原点处建立一个椭球
```
**************************************************************
[返回目录](#Outline)
### Evolved
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "join":"GeomAbs_Arc", # 连接类型 GeomAbs_Arc - 使用圆弧连接, GeomAbs_Tangent - 使用切线连接,GeomAbs_Intersection - 使用相交连接
    "axe_prof":True, # 是否轴向模式
    "solid":False, # 是否生成实体结果对象
    "prof_on_spine":False, # 轮廓线的坐标系原点是否位于脊线（Spine）上
    "tol":0.0000001 # 容差
}
```
- 输入图元:  
输入2个`OCC Shape`图元，1个Face和1个Wire,或者2个Wire


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建"演化"形状，即通过沿着一个路径扫掠一个轮廓并可能进行偏移操作来创建复杂形状
输入的2个图元有下面两种组合形式
1个Face和1个Wire: Face是轮廓,Wire是路径
2个Wire:第一个Wire是轮廓,第二个Wire是路径

prof_on_spine 参数的含义
True（默认值）
表示 轮廓线（Profile）的坐标系原点位于脊线（Spine）上。
扫掠时，轮廓线会 沿着脊线移动，并保持与脊线的相对位置关系。
适用于大多数标准扫掠情况，如管道、槽钢等。
False
表示 轮廓线（Profile）的坐标系原点不在脊线（Spine）上，而是保持 全局坐标不变。
扫掠时，轮廓线 不会跟随脊线的位置变化，而是保持原始位置，仅进行旋转或缩放（取决于其他参数）。
适用于某些特殊扫掠情况，如螺旋弹簧、变截面扫描等。
```
**************************************************************
[返回目录](#Outline)
### Face
- 类型: `Creator`


- 参数:  
```python
parameters = {
    "combin":False # 是否将多个结果组合返回
}
```
- 输入图元:  
多个`OCC Shape`图元,均为Wire类型


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
创建一个面对象
输入若干Wire类型图元
```
**************************************************************
[返回目录](#Outline)
### Fillet2D
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "r": 1.0, # 圆角半径
    "only_return_fillet": False # 是否只返回圆角对象, False表示传入的Edge对象也一并组合返回
}
```
- 输入图元:  
2个`OCC Shape`图元,均为Edge对象


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
建立二维圆角
传入的两个edge对象必须共面,并且必须有交点
```
**************************************************************
[返回目录](#Outline)
### Fillet
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "r" : 1, # 倒圆角半径
    "variable_filleting":False, # 是否使用半径列表创建可变半径的倒圆角
    "variable":[[0.0,1.0],[0.5,1.0],[1.0,1.0]], #半径列表
    "use_r2":False, # 是否使用第2半径
    "r2" : 1, # 倒圆角第2半径
    "use_ra": True, # 是否使用比例半径
    "ra": 0.15, # 比例值
    "batch": False, # True表示将传入的实体所有的边都倒角,即自动处理对象所有边而忽略给定的边,False表示根据参考对象选择需要倒角的边
    "combin": False # 多个对象是否以组合的方式返回
}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
多个`OCC Shape`图元


- 功能:  
```
给定对象倒圆角
输入的图元可能有以下几种情况:

batch为True时:
可以输入多个实体图元,将所有的边都倒圆角,无需输入参考图元
此时会使用r或者ra作为倒角半径的输入，variable_filleting和use_r2都不起作用，强制为False

batch为False时:
只输入1个需要倒角的图元,需要输入若干参考图元
参考图元的第1个对象和输入图元必须是同一个图元,其余图元是需要倒角的Edge对象
如果variable_filleting为True那么Edge对象的顺序和variable参数中的顺序保持一致

```
**************************************************************
[返回目录](#Outline)
### Fuse
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
布尔和操作
```
**************************************************************
[返回目录](#Outline)
### GlobalParamBatch
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "global_param_batch_list":[],
    "batch_return_result": True,
}
```
- 输入图元:  
[]


- 输出结果:  
[]


- 功能:  
```
TODO: 后续完善文档
```
**************************************************************
[返回目录](#Outline)
### Group
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "use_result_filter": False, # 开启结果过滤功能
    "exclude": False, # True表示return_index_list中的对象不是保留对象而是舍弃对象
    "return_index_list": [] # use_result_filter为True的情况下生效,表示需要保留的对象索引列表,其余对象会被舍弃
}
```
- 输入图元:  
若干个`OCC Shape`图元或其它任意类型对象


- 输出结果:  
若干个`OCC Shape`图元或其它任意类型对象


- 功能:  
```
将多个输入节点对象的输出进行组合
没有实际功能,起到一个分组的作用
use_result_filter 为True的情况下,可以通过return_index_list进行结果筛选
```
**************************************************************
[返回目录](#Outline)
### Helix
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "dx": 1.0, # dx/dy表示螺旋的斜度
    "dy": 1.0, # dx/dy表示螺旋的斜度
    "u1": 0.0, # u1到u2表示参数空间的范围，例如对于圆柱来讲u1=0,u2=6*pi则代表绕三圈的螺旋线
    "u2": 2.0, # u1到u2表示参数空间的范围，例如对于圆柱来讲u1=0,u2=6*pi则代表绕三圈的螺旋线
    "use_pi": True, # 表示u1和u2的数值传入后还要再与pi相乘才有效
    "make_wire": True # True表示返回为wire类型，否则为edge类型
}
```
- 输入图元:  
若干个`OCC Shape`图元,均为Face类型,一般输入1个Face,多个Face表示批量螺旋,Face一边是圆柱面或圆锥面


- 输出结果:  
若干个`OCC Shape`图元


- 功能:  
```
绘制螺旋线
输入图元为face对象，是建立螺旋线的参考曲面，如圆柱面,圆锥面等等，必须是Face类的对象
dx/dy表示螺旋的斜度
u1到u2表示参数空间的范围，例如对于圆柱来讲u1=0,u2=6*pi则代表绕三圈的螺旋线
```
**************************************************************
[返回目录](#Outline)
### Hyperbola
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "major_r": 2.0, # 实轴半长（从中心到顶点的距离，必须 >0）。
    "minor_r": 1.0, # 虚轴半长（与渐近线斜率相关，必须 >0）
    "scene": False # 方向标志(True表示沿主轴正方向构造双曲线)
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建一个双曲线
参考图元对象包含0到2个点,本质上是建立一个二维坐标系,表示主轴（gp_Ax2d 类型），定义双曲线的中心和开口方向
0个点：局部坐标系原点位于全局坐标系原点,法向量为y轴
1个点：局部坐标系原点位于给定参考点再xy平面的投影,法向量为y轴
2个点: 局部坐标系原点位于给定第1个参考点再xy平面的投影,法向量第2个点减去第1个点得到的向量在xy平面的投影
```
**************************************************************
[返回目录](#Outline)
### IFC
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "combin":True, # 是否返回组合图元
    "keep_color":True # 是否保持ifc文件中的颜色信息
}
```
- 输入图元:  
[]


- 输出结果:  
若干`OCC Shape`图元


- 功能:  
```
读取一个IFC文件
```
**************************************************************
[返回目录](#Outline)
### Iges
- 类型: `Loader`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
[]


- 输出结果:  
若干`OCC Shape`图元


- 功能:  
```
读取一个IGES文件
```
**************************************************************
[返回目录](#Outline)
### IncrementalMesh
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "linear_deflection": 0.9, # 线性偏差
    "angular_deflection": 0.5, # 角度偏差
    "is_relative": False, # 相对模式
    "is_in_parallel": True, # 是否启用并行计算加速网格化过程
    "self_adapt": True, # 是否使用自适应参数,True表示linear_deflection由包围盒算出,而不采用给定的值
    "bounding_box_tol": 0.001, # 包围盒容差
    "linear_deflection_coefficient":0.01 # 线性偏差系数self_adapt为True的情况下有用,使用自定义的包围盒最小尺寸再乘这个系数得到线性偏差
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
若干`OCC Shape`图元


- 功能:  
```
为拓扑形状(BRep)生成三角网格(triangulation)表示。

线性偏差 (LinearDeflection)
定义网格与实际曲面之间的最大允许距离值越小，网格越精细，计算时间越长
典型值范围：0.001-1.0 (取决于模型尺寸)

角度偏差 (AngularDeflection)
定义相邻三角面片法线之间的最大允许角度偏差(以弧度为单位)
控制曲面的细分程度
默认值：0.5弧度(约28.65度)
值越小，曲面细分越精细

is_relative
布尔值，决定偏差参数是绝对还是相对于形状尺寸,如果为True，偏差参数将相对于形状的边界框尺寸计算
```
**************************************************************
[返回目录](#Outline)
### Mirror
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "mirror_tool":"face", # 可选的镜像工具,包括 face,point,line
    "combin":True, # 结果是否组合成一个图元
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
镜像操作
mirror_tool取不同的值,则采用不同数量的参考点,一般采用face模式
point: 取1个参考点
line: 取2个参考点
face: 取3个参考点
```
**************************************************************
[返回目录](#Outline)
### Offset
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "combin": False, # 结果是否组合成一个图元返回
    "distance": 1.0, # 偏移距离, 正数向外,负数向内
    "both_side": False, # 内外两侧均产生偏移开关
    "join_type_arc": True # 线段交界处使用圆弧的开关
}
```
- 输入图元:  
若干`OCC Shape`图元, 应为Face或Wire对象


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
给定若干face对象或wire对象，进行二维偏移操作
mirror_tool取不同的值,则采用不同数量的参考点,一般采用face模式
point: 取1个参考点
line: 取2个参考点
face: 取3个参考点
```
**************************************************************
[返回目录](#Outline)
### OBoundingBox
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "the_is_triangulation_used":True, # 如果为 True，函数将使用形状的三角剖分数据来计算OBB,如果为 False将使用精确的几何表示
    "the_is_optimal":False, # 如果为 True，将计算更精确但更耗时的OBB, 如果为 False，使用更快的近似算法
    "the_is_shape_tolerance_used":True # 如果为 True，将考虑形状的容差来扩展 OBB 如果为 False，忽略形状容差
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
生成给定图元的方向包围盒
```
**************************************************************
[返回目录](#Outline)
### Parabola
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "focal": 5.0, # 抛物线焦距,即焦点到顶点的距离。
    "scene": False
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建一个抛物线
参考图元对象包含0到2个点,本质上是建立一个二维坐标系,表示主轴（gp_Ax2d 类型）,定义抛物线的对称轴和方向。
0个点：局部坐标系原点位于全局坐标系原点,法向量为y轴
1个点：局部坐标系原点位于给定参考点再xy平面的投影,法向量为y轴
2个点: 局部坐标系原点位于给定第1个参考点再xy平面的投影,法向量第2个点减去第1个点得到的向量在xy平面的投影
```
**************************************************************
[返回目录](#Outline)
### Pipe
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "combin": False, # 是否将结果组合成一个图元返回
    "is_shell": False, # True表示采用PipeShell模式进行扫掠
    "fill_modes_name": None, # Pipe操作的填充模式
    "force_approx_c1": True
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
针对传入的图元对象进行扫掠管道操作,本方法只支持一个轮廓,输入的多个shape会扫掠成多个对象
传入第1个对象为wire（Pipe的路径）,后面的对象均为shape,每个shape沿着路径进行pipe操作
需要我们的给定的起始截面的法线与路径在该处的切线一致

is_shell为True时,功能为：
使用BRepOffsetAPI_MakePipeShell ,但仍然只支持一个轮廓，如果要支持多轮廓，使用PipeShell算法节点

is_shell为False,功能为
BRepOffsetAPI_MakePipe 是一个更简单的管道生成工具，它基于 一个截面（Profile）沿一条路径（Spine）扫掠，生成一个连续的管道形状。它适用于较简单的管道建模，如圆形、矩形或自定义截面的管道。
fill_modes_name为如下字典的key
fill_modes = {
    'corrected_frenet': GeomFill_IsCorrectedFrenet,
    'fixed': GeomFill_IsFixed,
    'frenet': GeomFill_IsFrenet,
    'constant_normal': GeomFill_IsConstantNormal,
    'darboux': GeomFill_IsDarboux,
    'guide_ac': GeomFill_IsGuideAC,
    'guide_plan': GeomFill_IsGuidePlan,
    'guide_ac_contact': GeomFill_IsGuideACWithContact,
    'guide_plan_contact': GeomFill_IsGuidePlanWithContact,
    'discrete_trihedron': GeomFill_IsDiscreteTrihedron
}
force_approx_c1 参数的作用
问题背景：
BRepOffsetAPI_MakePipe 默认要求路径（Spine）至少是 G1 连续（切线连续），即路径曲线在连接点处方向平滑变化。
如果路径存在 尖锐拐角（G0 连续，仅位置连续），直接扫掠可能导致管道扭曲或生成失败。
参数功能：
当 force_approx_c1 = True 时，算法会对路径进行 自动平滑处理，通过插入过渡曲线（如圆弧或样条）近似实现 G1 连续性，从而保证管道生成的稳定性。
当 force_approx_c1 = False（默认值）时，若路径不满足 G1 连续，可能直接报错或生成畸形结果。
```
**************************************************************
[返回目录](#Outline)
### PipeShell
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "transition_modes_name": "Transformed", # 不同的过渡模式,三种选择 ["Transformed","RightCorner","RoundCorner"]
    "is_frenet": True # Frenet 模式：基于路径曲线的局部坐标系（Frenet 标架）确定截面方向，适用于光滑曲线。
}
```
- 输入图元:  
若干`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
扫掠管道（Pipe Shell）操作，即沿一条路径（ spine ）扫掠一个或多个轮廓（ profile ），生成复杂的三维几何体
传入多个图元,其中第一个Wire图元为路径,其余图元是按照顺序传入的轮廓
对比:
特性	        BRepOffsetAPI_MakePipe	BRepOffsetAPI_MakePipeShell
截面数量	    仅支持单截面	            支持多截面（可动态变化）
扫掠模式	    仅 Frenet 标架	        支持 Frenet、固定方向、自定义标架
适用场景	    简单管道（如水管、线缆）	    复杂管道（如变径管道、异形管道）
灵活性	    较低	                    高（可控制截面过渡、旋转等）
```
**************************************************************
[返回目录](#Outline)
### Polygon
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,1],[0,1,1],[0,1,0]]
parameters = {
    "close": True # 多边形自动封闭
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
给定若干参考点，形成一个多边形
```
**************************************************************
[返回目录](#Outline)
### Prism
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,1]]
parameters = {
    "height": 1.0, # 拉伸高度,可以为负数
    "use_height": False, # True表示使用给定的拉伸高度,False表示使用前两个参考点给出的距离作为拉伸高度
    "auto_normal_vector": False, # True自动创建法向量作为方向向量，False表示使用传入的两个参考点作为方向向量
    "combin": False # 拉伸结果是否组合
}
```
- 输入图元:  
1个或多个`OCC Shape`图元,应该为Face图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
拉伸操作
传入的参考图元为点图元分为以下两种情况
传入2个点或传入0个点:
当传入0个点时,必须将use_height设为True,auto_normal_vector也设为True
```
**************************************************************
[返回目录](#Outline)
### ProjectCurve
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "combin": False # 结果是否组合
}
```
- 输入图元:  
2个`OCC Shape`图元,应该一个为Vertex,另一个为曲线形成的Edge,或Wire


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
点在曲线上的投影点
给定点和曲线，返回点在曲线上的投影点
输入的edge必须是曲线，如果是一般的线则无效
```
**************************************************************
[返回目录](#Outline)
### ProjectSurf
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "combin": False # 结果是否组合
}
```
- 输入图元:  
2个`OCC Shape`图元,应该一个为Vertex,另一个为Face


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
点在曲面上的投影点
给定点和曲面，返回点在曲面上的投影点
```
**************************************************************
[返回目录](#Outline)
### PyScript
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "render_parameters": None # Render参数字典
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
执行render类型的py脚本,一般不使用此方法
```
**************************************************************
[返回目录](#Outline)
### Remove
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
1个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
删除一个图元中的几何特征
要删除的几何特征用参考图元的方式指定
参考图元的第1个对象和输入图元必须是同一个图元,其余图元是需要删掉的特征
```
**************************************************************
[返回目录](#Outline)
### ResultFilter
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "use_assist_shapes":False, # 使用assist_shapes_id_list筛选模式生效
    "exclude":False, # 是否使用排除模式
    "return_index_list":[], # 需要返回的索引列表

    "use_index_express":False, # 索引区间筛选模式生效
    "index_start" :0, # 起始索引
    "has_index_start" :False, #是否使用起始索引
    "index_end":0, # 终止索引
    "has_index_end":False, # 是否使用终止索引
    "index":0, # 唯一索引 在has_index_start和has_index_end都为False时才有效

    "shape_type_filter":False, # 使用OCC类型筛选的开关
    "shape_type_list":[], # 需要保留的OCC Shape类型名称列表
}
```
- 输入图元:  
任意数量任意类型的算法流水线节点


- 输出结果:  
任意数量任意类型的结果对象


- 功能:  
```
结果过滤器
并无具体处理功能，而是将输入节点的所有结果进行筛选

shape_type_filter 为 False时, use_assist_shapes才可能生效, 当use_assist_shapes 为False时use_index_express才可能生效
基本逻辑组合如下
1. shape_type_filter为True:
shape_type_list存放了需要保留的图元类型,主要用来筛选OCC图元的
shape_type_list是一个list,可以存放多种类型,可选的类型名称["solid","shell","face","wire","edge","vertex","shape","compound","compsolid"]

2. shape_type_filter为False, use_assist_shapes为True
通过assist_shapes_id_list中的指定id来筛选输入的对象,例如
assist_shapes_id_list = [{"index":2, "shape_type":None},{"index":7, "shape_type":None}] 表示保留输入的对象中的第2个和第7项的结果，其余舍弃
注意 exclude 为True时,则恰恰相反,丢弃assist_shapes_id_list中的索引对应的结果,而是保留剩下的结果

3. shape_type_filter为False, use_assist_shapes为False, use_index_express为True
表示通过index_start和index_end形成的区间来截取输入的对象的内容

4. 其它情况
直接使用 return_index_list 中的索引来获取结果内容
此时exclude参数也生效
```
**************************************************************
[返回目录](#Outline)
### Revol
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,1]]
parameters = {
    "d": None, #旋转角度（以弧度为单位）2*PI表示完整旋转（360度）可以指定小于2*PI的角度创建部分旋转体
    "copy": False, # 如果为True，会创建输入形状的副本进行旋转，如果为False，直接使用原始形状
    "combin": False # 是否结果组合
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
进行旋转操作
需要2个参考点定义旋转轴向量，从第1个点指向第2个点
```
**************************************************************
[返回目录](#Outline)
### RingArray
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r": 1.0, # 阵列半径
    "alpha1":0.0, # 起始角度
    "alpha2":3.14, # 终止角度
    "use_arc":False,  # 是否圆弧阵列的开关,True的时候alpha1和alpha2两个角度才有效
    "number":3, # 阵列数量
    "combin": True # 是否组合阵列结果
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
环形阵列
需要至少1个参考点,参考点规则如下
参考点中的第一个点是基点
参考点中其余包含若干点(0-3个点有效)，决定了绘制圆的模式
包含：
0个点：圆心在原点，圆所在法向量为z轴
1个点：圆心在第1个点，圆所在法向量为z轴
2个点: 圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向
3个点：圆心在第1个点，圆所在法向量为第1个点到第2个点的连线方向，新坐标系x轴为第1个点指向第三个点的方向
```
**************************************************************
[返回目录](#Outline)
### Rotate
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "rx": 0.0, # x轴旋转角度
    "ry": 0.0, # y轴旋转角度
    "rz": 0.0, # z轴旋转角度
    "deg": True, # 是否角度的开关,否则为弧度
    "combin": True # 是否组合多个旋转结果
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
简单旋转变换,
如果有1个参考点,那么该点为旋转基点,(有基点的情况下可以认为将基点先平移到原点，再绕原点旋转,再将基点平移到原来位置)
如果没有参考点,那么绕原点旋转
```
**************************************************************
[返回目录](#Outline)
### Scale
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "scaling_value": 1.0, # 缩放比例
    "use_ref_vertex_to_scaling": True, # True表示使用参考点距离来给定缩放比例，需要4个参考点
    "combin": True # 是否组合多个结果
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
缩放变换
需要给出参考点
第1个参考点为缩放中心
如果use_ref_vertex_to_scaling为True,那么就还需要4个参考点,而缩放比例也可以通过四个点形成的两条线的长度比例来计算
```
**************************************************************
[返回目录](#Outline)
### Script
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "script": None, # 脚本内容
    "arg1": "value1" # 用户自定义变量，支持多个
    
}
```
- 输入图元:  
1个或多个任意类型对象


- 输出结果:  
1个或多个任意类型对象


- 功能:  
```
支持python脚本,进行定制化复杂操作
```
**************************************************************
[返回目录](#Outline)
### Section
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
2个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
计算两个形状之间的截面（交叉部分）
输入可以是任意拓扑形状（如 TopoDS_Shape），包括实体（Solid）、曲面（Surface）、面（Face）、边（Edge）等。
输出结果为两个形状的交集（通常是一个边或点的集合，表示交线或交点）。
```
**************************************************************
[返回目录](#Outline)
### Shell
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元,均为Face


- 输出结果:  
1个`OCC Shape`图元，为Shell


- 功能:  
```
将多个Face拼接成Shell
```
**************************************************************
[返回目录](#Outline)
### Solid
- 类型: `Operator`


- 参数:  
```python
parameters = {}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元, 为Solid


- 功能:  
```
将多个Face拼接成Shell
输入图元可以有下面几种情况: 
1. 可以为1到3个Shell
2. 1个CompSolid
3. 1个Solid和多个Shell
```
**************************************************************
[返回目录](#Outline)
### Sphere
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r": 10, # 球半径
    "angle1": None, # 角度1
    "angle2": None, # 角度2
    "angle3": None # 角度3
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建球
如果参考点中不包含点，则在原点处创建
如果参考点包含1个点，则在该点处创建
如果参考点包含2个点，则以该点为轴创建
如果参考点包含3个点，则根据三个点所创建的坐标系统来创建
```
**************************************************************
[返回目录](#Outline)
### SpherialCrown
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r": 10, # 球半径
    "h": 5, # 球冠高度
    "reverse": False # 反向
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建球冠
如果参考点中不包含点，则在原点处创建
如果参考点包含1个点，则在该点处创建
如果参考点包含2个点，则以该点为轴创建
如果参考点包含3个点，则根据三个点所创建的坐标系统来创建
```
**************************************************************
[返回目录](#Outline)
### Split
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "combin":False, # 组合结果
    "decompose":True # 分解结果
}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
将传入图元进行分割操作并返回分割结果
其中第一个edge对象或face为分割工具对象，其余对象均为被分割对象
如果第一个对象是edge对象，那么后面要分割的就一定是面，
如果第一个对象是face对象，后面可以分割任意对象
```
**************************************************************
[返回目录](#Outline)
### SplitByPlane
- 类型: `Operator`


- 参数:  
```python
coordinates = [[0,0,0],[0,0,1],[0,1,1]]
parameters = {
    "use_normal":False, # True使用点和法线的方式定义分割平面,False表示使用三点确定平面的方式确定分割平面
    "reverse":False, # 保留反向部分
    "combin":False # 组合结果
}
```
- 输入图元:  
多个`OCC Shape`图元


- 输出结果:  
多个`OCC Shape`图元


- 功能:  
```
平面切割对象
参考中如果包含三个点并且use_normal=False，则利用三个点创建平面分割图元
如果use_normal=True，则利用传入的参考点中的3个点或2个点创建点和法向量，一次来分割图元
具体规则是
传入2个参考点,第1个点是平面内1点,第1个点到第2个点的向量定义为法向量
传入3个参考点,第1个点是平面内1点,第2个点到第3个点的向量定义为法向量
```
**************************************************************
[返回目录](#Outline)
### SplitEdge
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "point_number":1, # 分割点数
    "combin":False # 组合结果
}
```
- 输入图元:  
1个`OCC Shape`图元,为Edge类型


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
将线段进行分点操作
```
**************************************************************
[返回目录](#Outline)
### Step
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "read_color_and_names":True, # 读取step文件中的零件名和颜色
    "keep_color":True # 保留step文件中定义的颜色
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
读取step格式文件
```
**************************************************************
[返回目录](#Outline)
### Stl
- 类型: `Loader`


- 参数:  
```python
parameters = {
    "fast_load":False # True能提升加载速度快速显示外形结果,但是结果不能后续进行任何CAD操作 
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
读取stl格式文件
```
**************************************************************
[返回目录](#Outline)
### STLWriter
- 类型: `Writer`


- 参数:  
```python
parameters = {
    "file_format_ascii": True, # True表示使用ascii存储stl,False表示使用二进制
    "shape_combination": True # 表示将图元组合
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
DOMAIN_TYPE_WRITER_FILES 格式对象,并且保存stl格式文件


- 功能:  
```
将图元写成stl格式文件
```
**************************************************************
[返回目录](#Outline)
### SVGWriter
- 类型: `Writer`


- 参数:  
```python
parameters = {
    "shape_combination": True,
    "use_coordinates": True,
    "location": [0, 0, 0],
    "direction": [1, 1, 1],
    "width": 800,
    "height": 600,
    "margin_left": 10,
    "margin_top": 30,
    "export_hidden_edges": True,
    "svg_color": "black",
    "line_width": "1px",
    "unit": "mm"
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
DOMAIN_TYPE_WRITER_FILES 格式对象,并且保存svg格式文件


- 功能:  
```
将图元写成svg格式文件
use_coordinates 为True时
参考点第1个为location
如果有第2个点，而没有第3个点
参考点第2个减去第1个的向量为direction
如果有第3个点
参考点第3个减去第2个的向量为direction

svg是平面矢量文件,因此实际上保存的只是一个视角的投影
width, height (optional): integers, specify the canva size in pixels
margin_left, margin_top (optional): integers, in pixel
export_hidden_edges (optional): whether or not draw hidden edges using a dashed line
location (optional): a gp_Pnt, the lookat
direction (optional): to set up the projector direction
svg_color (optional), "default to "black".
line_width (optional, default to 1): an integer
```
**************************************************************
[返回目录](#Outline)
### Tetrahedron
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0],[1,1,0],[1,0,0],[0,0,1]]
parameters = {}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
生成四面体
通过给定4个参考点实现
```
**************************************************************
[返回目录](#Outline)
### ThickSolid
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "offset": 1.0, # 偏移距离，可以为正数或者负数
    "tol": 0.001, # 容差
    "intersection": False, # 相交模式
    "self_inter": False, # 允许自相交
    "join_arc": True # True弧线连接，False 非弧线连接
}
```
- 输入图元:  
1个`OCC Shape`图元


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
抽壳操作
用于从壳体(Shell)或实体(Solid)创建厚度均匀的实体。
输入形状必须是水密的(无缝隙)
过大的偏移值可能导致自相交或失败
复杂形状可能需要调整容差以获得满意结果

需要参考图元定义移除面,
参考图元的第1个对象和输入图元必须是同一个图元,其余图元是需要移除的Face对象
```
**************************************************************
[返回目录](#Outline)
### Thrusections
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "is_solid":False, # 是否生成实体（True）或壳体（False）
    "ruled":True # 是否创建直纹面（True）或平滑过渡（False）
}
```
- 输入图元:  
多个`OCC Shape`图元 必须为Wire或Vertex对象


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
放样
通过一系列截面轮廓创建过渡曲面（放样曲面），生成实体或壳体。
进行ThruSections操作，输入对象为一系列的Wire或Vertex,按照顺序排列
```
**************************************************************
[返回目录](#Outline)
### ThrusectionsPath
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "is_solid":False, # 是否生成实体（True）或壳体（False）
    "ruled":True, # 是否创建直纹面（True）或平滑过渡（False）
    "return_wires": False,
    "rotate_to_fit_path": True, # 阵列过程是否适应path_line的走向
    "cur_axis_mode": "pre_cur",
    "axis_type": "x",
    "gen_type": "y",
    "project_plane": "xy",
    "reverse": False
}
```
- 输入图元:  
多个`OCC Shape`图元 必须为Wire对象


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
路径放样
将多个Wire对象进行ThruSections操作，输入对象为第一个Wire为被操作对象，
通过参考点可输入若干个点
第一个点为移动基点，第二个点为旋转基点，后面一系列的点对象作为路径点
具体操作逻辑为将点对象形成path，每个节点做一个被操作对象的平移克隆，然后按路径方向偏转
# rotate_to_fit_path为真时下面参数有效，存放了跟旋转相关的信息
"axis_type" : "x",      # path_line所体现的方向向量v1代表旋转坐标系的那个哪个轴，可以从'x'，'y'，'z'中进行选择
"gen_type" : "y" ,      # 我们将会根据path_line所体现的方向向量v1再创建一个与它正交的坐标轴v2，这个轴是坐标系中的哪一个轴用gen_type
                        # 来指定，可以从'x'，'y'，'z'中进行选择
"project_plane":"xy",   # 根据path_line方向向量v1生成旋转坐标系(v1,v2,v3)的策略是，将该v1向一个投影面投影，该投影面由project_plane
                        # 指定，可以从'xy'，'yz'，'xz'中进行选择。生成投影向量vp，v1和vp能形成一个平面P，
                        # 我们是期望新坐标系中有一个坐标轴就是在平面P之内的，而且与v1是正交的，这个轴就是v3。但是v3如何求得呢？
                        # 我们这样做，先将v1和vp做叉乘，这样就得到了向量v2也就是我们的第2个坐标轴，这样不仅可以保证v2与v1是正交的，
                        # 而且还能保证v2垂直于P，这说明了v1，v2形成的坐标系的第三个轴v3一定落在P内，也就是满足了我们的期望了
"reverse":false,        # v2坐标轴生成后，可以利用reverse字段判断是否要反向
"axis_mode": "cur_post" # axis_mode现在有两种选项"cur_post"和"pre_cur"。表示了我们如何通过path_line构建v1
                        # cur_post字段表示我们将path_line进行分割后形成点集合，一个点的方向向量由它自身和下一个点的位置确定
                        # pre_cur字段表示我们将path_line进行分割后形成点集合，一个点的方向向量由它上一个点和自身的位置确定
```
**************************************************************
[返回目录](#Outline)
### Torus
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "r1":10, # 外半径
    "r2":5, # 内半径
    "angle":None, # 角度(弧度)
    "angle1":None, # 起始角度(弧度)
    "angle2":None, # 终止角度(弧度)
    "fitting_type":False # 自适应模式开关
}
```
- 输入图元:  
[]


- 输出结果:  
1个`OCC Shape`图元


- 功能:  
```
创建一个圆环

如果参考点中不包含点，则在原点处创建
如果参考点包含1个点，则在该点处创建
如果参考点包含2个点，则以该点为轴创建
如果参考点包含3个点，则根据三个点所创建的坐标系统来创建

如果fitting_type为True时,使用拟合模式创建圆环，此时要求传入图元中必须传入3个点
其中第1个点为起始点，第三个点为中止点，第二个点为中间点。根据三个点确定一条折线，而与折线两端都相切的圆弧为圆环的中心轴线
然后根据r1和r2画出圆环
```
**************************************************************
[返回目录](#Outline)
### Transform
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "transform_matrix": [1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0] # 坐标变换矩阵
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
变换
使用坐标变换矩阵进行变换
```
**************************************************************
[返回目录](#Outline)
### TransformRT
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "use_coordinates": True, # 是否使用参考点的开关
    "rotate_base_point": [0, 0, 0], # 旋转基点
    "translation": [0, 0, 0], # 平移向量
    "axis_x": [1, 0, 0], # 目标坐标系x轴
    "axis_y": [0, 1, 0], # 目标坐标系y轴
    "axis_z": [0, 0, 1], # 目标坐标系z轴
    "mode": "xyz", # 目标坐标系定义模式,包括了4种模式,"xyz","xy","xz"和"yz"
    "orthogonality": False,
    "fixed_axis": "z", 
    "inverse_matrix": False, # 逆矩阵开关
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
针对目标坐标系的旋转变换,不直接指定旋转角度，而是知道旋转后的坐标系定义，将给定图元进行旋转

方法核心是生成一个坐标变换矩阵4×4，该矩阵的操作是，先从rotate_base_point指定的旋转基点移动到原点，
然后进行旋转，然后再从原点移回基点，然后再根据translation给出的向量移动到指定位置
axis_x，axis_y，axis_z是变换坐标系的三个轴，要求正交
mode包括了4种模式,"xyz","xy","xz"和"yz",分别表示需要提供的对应的axis_x，axis_y，axis_z
换句话说，如果用户只提供了两个坐标轴的数据axis_x和axis_z，则需要将mode设置为xz，
这样函数会自动计算出axis_y

支持参考点传入
第1个定义 rotate_base_point
第2个定义 translation
axis_x 向量由第1个点指向第3个点
axis_y 向量由第1个点指向第4个点
axis_z 向量由第1个点指向第5个点
```
**************************************************************
[返回目录](#Outline)
### TransformRTAS
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "use_coordinates": True, # 是否使用参考点的开关
    "translation": [0, 0, 0], # 平移向量
    "axis_z": [0, 0, 1] # 目标坐标系z轴
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
TransformRT 的快速简化版本,有一定限制，只适合对称物体，如圆柱。
其原因是只变换坐标系（新坐标系）的z轴，而只靠一个轴是不能精确确定
一个坐标系的，至少还需要一个轴的信息，因此变换的结果只有z轴是确定的，而
x轴和y轴的位置是不确定的。

axis_z 来自第1个参考点
translation 来自第2个点指向第3个点
```
**************************************************************
[返回目录](#Outline)
### Translation
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "use_vector": False, # 是否直接指定平移向量
    "vector": [0.0, 0.0, 0.0], # 平移向量
    "combin": True # 组合结果
}
```
- 输入图元:  
1个或多个`OCC Shape`图元


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
平移操作
如果use_vector为False,那么平移向量从参考点获得，来自第1个点指向第2个点
```
**************************************************************
[返回目录](#Outline)
### Vector
- 类型: `Operator`


- 参数:  
```python
parameters = {
    "operate_type": "add", # 向量计算操作类型支持["add","cross","dot","multiply","subtract","distance","angle","normal","trans","unitization",]
    "unitization": False, # 是否单位化
    "trans_matrix":[[1,0,0,0],[0,1,0,0],[0,0,1,0],[0,0,0,1]], # 变换矩阵
    "n":1.0 # 数乘
}
```
- 输入图元:  
1个或多个`OCC Shape`图元，都是Vertex对象


- 输出结果:  
1个或多个`OCC Shape`图元，都是Vertex对象


- 功能:  
```
对传入的两个向量进行如下操作
"add": 两个向量和
"cross": 两个向量叉乘
"dot": 两个向量点乘，结果为一个点，坐标为[0,0,结果]
"multiply": 第一个向量数乘
"subtract": 两个向量差
"distance": 两个向量距离，结果为一个点，坐标为[0,0,结果]
"angle": 两个向量夹角，结果为一个点，坐标为[0,0,结果（弧度）]
"normal": 两个向量的法向量
"trans": 第一个向量根据矩阵变换
"unitization": 第一个向量归一化

传入的向量既可以以参考点的方式给出，也可以以输入图元的方式给出,其读取顺序是先读取参考点，再读取输入图元
```
**************************************************************
[返回目录](#Outline)
### Vertex
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "x": 0,
    "y": 0,
    "z": 0,
    "combin": False # 是否组合结果
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元, Vertex类型


- 功能:  
```
创建若干个点对象,优先使用参考点坐标，没有参考点则使用给定的x,y,z生成1个点
```
**************************************************************
[返回目录](#Outline)
### Wedge
- 类型: `Creator`


- 参数:  
```python
coordinates =  [[0,0,0]]
parameters = {
    "dx":4,
    "dy":4,
    "dz":8,
    "ltx":1,
    "xmin":1,
    "zmin":1,
    "xmax":3,
    "zmax":7,
    "use_ltx":True
}
```
- 输入图元:  
[]


- 输出结果:  
1个或多个`OCC Shape`图元


- 功能:  
```
创建楔形体
如果参考点中不包含点，则在原点处创建
如果参考点包含1个点，则在该点处创建
如果参考点包含2个点，则以该点为轴创建
如果参考点包含3个点，则根据三个点所创建的坐标系统来创建
```
**************************************************************
[返回目录](#Outline)
### Wire
- 类型: `Operator`


- 参数:  
```python
coordinates =  [[0,0,0],[0,0,1],[0,1,1]]
parameters = {
    "vertex_input":True, # 使用点作为输入,False表示使用edge作为输入
    "edge_sort":False, # 边排序,True表示会将edge按照首尾相接的方式排序
    "linear_to_lerance":0.000001, # edge_sort为True有效,表示边排序时共点判断的精度
}
```
- 输入图元:  
1个或多个`OCC Shape`图元，Vertex类型或Edge类型


- 输出结果:  
1个`OCC Shape`图元, Wire类型


- 功能:  
```
将传入的点对象组合成wire
当vertex_input为True时
支持参考点传入,也支持输入图元传入,参考点在前，图元在后，图元均为Vertex
当vertex_input为False时
支持输入图元传入,图元均为Edge
```
