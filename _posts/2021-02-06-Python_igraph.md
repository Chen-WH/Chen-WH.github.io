---
layout:     post
title:      Igraph 
subtitle:   Python-Igraph 
date:       2021-02-06
author:     ChenWH 
header-img: img/the-first.png
catalog:   true
tags:

    - Visualization
---



<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ["\\(","\\)"] ],
	    processEscapes: true
	  }
	});
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

# Python-igraph

**官网：**[Tutorial (igraph.org)](https://igraph.org/python/doc/tutorial/tutorial.html#igraph-and-the-outside-world)

-----

## 创建图形

```python
g = Graph()
g = Graph.Full(10)  #满边图
#添加顶点
add_vertices(3)
#添加边
add_edges()[(0,1), (1,2)]
#顶点数、边数和图形的名称；summary略去边列表
print(g)
summary(g)
#删除边及顶点
delete_edges(2)
delete_vertices(2)
#获取顶点之间边的ID
get_eid(3,5)
```



----

## 生成图形

```python
#生成常规树图,我们生成的顶点有 127 个顶点，每个顶点（叶子除外）有两个子级
Tree(127, 2)
#边列表(前十个)
get_edgelist()[0:10]
#几何随机图，在单位正方形内随机均匀地选择 n 点，并且与预定义距离d通过边连接
GRG(n,d)
#返回两个图形是否同构，通常需要相当长的时间
g.isomorphic(g2)
```



-----

## 设置和检索属性

```python
g = Graph([(0,1), (0,2), (2,3), (3,4), (4,2), (2,5), (5,0), (6,3), (5,6)])
g.vs["name"] = ["Alice", "Bob", "Claire", "Dennis", "Esther", "Frank", "George"]
g.es["is_formal"] = [False, False, True, True, True, False, True, False, False]
#查看属性
g.es[0].attributes()
#索引调用
g.es[0]
g.es[0]["is_formal"] = True
#图的属性
g["date"] = "2009-01-10"
#删除属性
del g.vs["foo"]
```



-----

## 图形的结构属性

```python
g.degree()
[3, 1, 4, 3, 2, 3, 2]
g.degree(6)
2
g.degree([2,3,4])
[4, 3, 2]
#入度和出度
g.degree(mode='in'), g.degree(mode='out')
g.indegree(), g.outdegree()
```

|    指标名称    |                             概念                             |                             比较                             |        实际应用        |     函数      |
| :------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :--------------------: | :-----------: |
|   点度中心度   |                    在某个点上，有多少条线                    |                      强调某点单独的价值                      |    作为基本点的描述    |   degree()    |
|   接近中心度   | 该点与网络中其他点距离之和的倒数，越大说明越在中心，越能够很快到达其他点 |              强调点在网络的价值，越大，越在中心              |   基本描述，用户价值   |  closeness()  |
|   中间中心度   | 代表最短距离是否都经过该点，如果都经过说明这个点很重要，其中包括线的中心度 |    强调点在其他点之间调节能力，控制能力指数，中介调节效应    | 推荐算法，用户的控制力 | betweenness() |
| 特征向量中心度 | 根据相邻点的重要性来衡量该点的价值。首先计算邻接矩阵，然后计算邻接矩阵的特征向量。 | 强调点在网络的价值，并且比接近中心度厉害的是，点价值是根据近邻点来决定的 | 推荐算法，用户潜在价值 |   evcent()    |



-----

## 基于属性查询顶点和边

### 选择顶点和边

```python
g.vs.select(_degree = g.maxdegree())["name"]
["Alice", "Bob"]
##
graph = Graph.Full(10)
only_odd_vertices = graph.vs.select(lambda vertex: vertex.index % 2 == 1)
len(only_odd_vertices)
##
>>> seq = graph.vs.select([2, 3, 7])   ##生成类型可视为子图
>>> len(seq)
3
>>> [v.index for v in seq]
[2, 3, 7]
>>> seq = seq.select([0, 2])
>>> [v.index for v in seq]
[2, 7]
#所有边（源顶点索引 2）
g.es.select(_source=2)
#选定点之间的所有边
g.es.select(_within=g.vs[2:5])
#选择源自其中一个集并终止于另一个集的所有边
men = g.vs.select(gender="m")
women = g.vs.select(gender="f")
g.es.select(_between=(men, women))
##
g.vs.select(age_lt=30)
```

关键字参数

| Keyword argument |                         **Meaning**                          |
| :--------------: | :----------------------------------------------------------: |
|     name_eq      |             属性/属性值*必须等于*关键字参数的值              |
|     name_ne      |             属性/属性*值不能等于*关键字参数的值              |
|     name_lt      |             属性/属性值*必须小于*关键字参数的值              |
|     name_le      |          属性/属性值*必须小于或等于*关键字参数的值           |
|     name_gt      |             属性/属性值*必须大于*关键字参数的值              |
|     name_ge      |          属性/属性值必须*大于或等于*关键字参数的值           |
|     name_in      | 属性/属性值必须*包含在关键字*参数的值中，在这种情况下，该值必须是序列 |
|    name_notin    | 属性/属性值一定不能包含在关键字参数的值中，在这种情况下，该值必须是一个序列 |



-----

### 查找具有某些属性的单个顶点或边

```python
g.vs.find(name="Claire")
```



-----

### 按名称查找顶点

```python
#iGraph接受顶点名称（几乎）任何需要顶点 ID 的任何地方，并且也接受顶点名称的集合（列表等）
g.degree("Dennis")
g.vs.find("Dennis").degree()
```



----

## 将图形视为邻接矩阵

```python
g.get_adjacency()
```



----

## 布局和绘图

### 布局算法

|             方法名称             |               短名称               |                           算法描述                           |
| :------------------------------: | :--------------------------------: | :----------------------------------------------------------: |
|          layout_circle           |         circle`,`circular          |                  将顶点放在圆上的确定性布局                  |
|            layout_drl            |                drl                 | The Distributed Recursive Layout algorithm for large graphs  |
|   layout_fruchterman_reingold    |                 fr                 |        Fruchterman-Reingold force-directed algorithm         |
|  layout_fruchterman_reingold_3d  |            fr3d`,`fr_3d            |    Fruchterman-Reingold force-directed algorithm（三维）     |
| layout_grid_fruchterman_reingold |              grid_fr               | Fruchterman-Reingold force-directed algorithm （带大型图形的网格启发式） |
|       layout_kamada_kawai        |                 kk                 |            Kamada-Kawai force-directed algorithm             |
|      layout_kamada_kawai_3d      |           kk3d`, `kk_3d            |        Kamada-Kawai force-directed algorithm（三维）         |
|            layout_lgl            |    large`, `lgl`, `large_graph     |      The Large Graph Layout algorithm for large graphs       |
|          layout_random           |               random               |                       顶点位置完全随机                       |
|         layout_random_3d         |             random_3d              |                     顶点位置三维完全随机                     |
|     layout_reingold_tilford      |             rt`, `tree             |      Reingold-Tilford树 布局，可用于（大部分）树状图形       |
| layout_reingold_tilford_circular |          rt_circular tree          | Reingold-Tilford树布局，具有极坐标转换后，可用于（大部分）树状图形 |
|          layout_sphere           | sphere`, `spherical`, `circular_3d |            将顶点均匀地放在球体表面上的确定性布局            |

```python
layout = g.layout_kamada_kawai()
layout = g.layout("kamada_kawai")

layout = g.layout_reingold_tilford(root=[2])
layout = g.layout("rt", [2])
```



----

### 使用布局绘制图形

```python
layout = g.layout("kk")
plot(g, layout = layout)
## 300 x 300 像素和图形周围的较大边距来适应标签（20 像素）
g.vs["label"] = g.vs["name"]
color_dict = {"m": "blue", "f": "pink"}
g.vs["color"] = [color_dict[gender] for gender in g.vs["gender"]]
plot(g, layout = layout, bbox = (300, 300), margin = 20)
## 如果要将图形的可视表示属性与图形本身分开，则操作如下
color_dict = {"m": "blue", "f": "pink"}
plot(g, layout = layout, vertex_color = [color_dict[gender] for gender in g.vs["gender"]])
##设置一个 Python 字典，其中包含要传递到的关键字参数，然后使用**运算符将特定样式属性传递到plot()
visual_style = {}
visual_style["vertex_size"] = 20
visual_style["vertex_color"] = [color_dict[gender] for gender in g.vs["gender"]]
visual_style["vertex_label"] = g.vs["name"]
visual_style["edge_width"] = [1 + 2 * int(is_formal) for is_formal in g.es["is_formal"]]
visual_style["layout"] = layout
visual_style["bbox"] = (300, 300)
visual_style["margin"] = 20
plot(g, **visual_style)
```



----

### 顶点属性

|  属性名称   |     关键字参数     |                             目的                             |
| :---------: | :----------------: | :----------------------------------------------------------: |
|    color    |    vertex_color    |                          顶点的颜色                          |
|    label    |    vertex_label    |                          顶点的标签                          |
| label_angle | vertex_label_angle | 顶点标签在圆顶点周围的位置。这是弧度中的一个角度，零在顶点的右侧。 |
| label_color | vertex_label_color |                        顶点标签的颜色                        |
| label_dist  | vertex_label_dist  |                 顶点标注相对于顶点大小的距离                 |
| label_size  | vertex_label_size  |                      顶点标签的字体大小                      |
|    order    |    vertex_order    |      顶点的绘制顺序。将首先绘制具有较小顺序参数的顶点。      |
|    shape    |    vertex_shape    | 顶点的形状。例如rectangle`, `circle`, `hidden`, `triangle-up`, `triangle-down。更多支持形状见drawing.known_shapes |
|    size     |    vertex_size     |                  顶点的大小（以像素为单位）                  |



-----

### 边属性

|  属性名称   |    关键字参数    |                             目的                             |
| :---------: | :--------------: | :----------------------------------------------------------: |
|    color    |    edge_color    |                           边的颜色                           |
|   curved    |   edge_curved    | 边的曲率。正值对应于 CCW 方向弯曲的边，负数对应于顺时针 （CW） 方向弯曲的边。零表示直边。 True为 0.5，False为0。这可用于使多个边可见。另请参阅 autocurve 关键词。 |
| arrow_size  | edge_arrow_size  |     如果图形是有向的，则边缘箭头的长度，相对于 15 像素。     |
| arrow_width | edge_arrow_width |    如果图形是有向的，则边缘上的箭头宽度，相对于 10 像素。    |
|    width    |    edge_width    |                   边的宽度（以像素为单位）                   |



-----

### plot关键词

| 关键字参数 |                             目的                             |
| :--------: | :----------------------------------------------------------: |
| autocurve  | 是否在具有多个边的图形中自动确定边形的曲率。默认值为True对于小于 10000 边的图形，否则False。 |
|    bbox    | 绘图的界框。必须是包含所需的宽度和绘图高度的元组。默认绘图为 600 像素宽，600 像素高。 |
|   layout   | 要使用的布局。它可以是Layout的一种，包含 X-Y 坐标的元组列表或布局算法的名称。默认值为auto，它根据图形的大小和连接性自动选择布局算法。 |
|   margin   | 绘图的顶部、右侧、底部和左侧边距（以像素为单位）。此参数必须是列表或元组，如果指定的列表或元组少于四个元素，将重复使用其元素。 |



-----

### 在绘图中指定颜色

参考wiki:[X11 color names - Wikipedia](https://en.wikipedia.org/wiki/X11_color_names)

或者 igraph.drawing.colors.known_colors

此外颜色名称在 igraph 中不区分大小写

1. `#RRGGBB`, 范围从 0 到 255 的十六进制格式。 Example: `"#0088ff"`
2. `#RGB`, 范围从 0 到 15 的十六进制格式。 Example: `"#08f"`
3. `rgb(R, G, B)`, 范围从 0 到 255 或从 0% 到 100%。Example: `"rgb(0, 127, 255)"` or `"rgb(0%, 50%, 100%)"`



-----

### 保存图

````python
plot(g, "test.png", **visual_style)
````



----

## *igraph*和外部世界

**可以读取或写入的格式**

|       格式       |        短名称        |        读取方法        |        编写方法         |
| :--------------: | :------------------: | :--------------------: | :---------------------: |
|     邻接列表     |         lgl          |    Graph.Read_Lgl()    |    Graph.write_lgl()    |
|     邻接矩阵     |      adjacency       | Graph.Read_Adjacency() | Graph.write_adjacency() |
|      DIMACS      |        dimacs        |  Graph.Read_DIMACS()   |  Graph.write_dimacs()   |
|        DL        |          dl          |    Graph.Read_DL()     |       尚未受支持        |
|      边列表      | edgelist, edges,edge | Graph.Read_Edgelist()  | Graph.write_edgelist()  |
|     GraphViz     |     graphviz,dot     |       尚未受支持       |    Graph.write_dot()    |
|       GML        |         gml          |    Graph.Read_GML()    |    Graph.write_gml()    |
|     GraphML      |       graphml        |  Graph.Read_GraphML()  |  Graph.write_graphml()  |
| Gzipped GraphML  |       graphmlz       | Graph.Read_GraphMLz()  | Graph.write_graphmlz()  |
|       LEDA       |         leda         |       尚未受支持       |   Graph.write_leda()    |
| Labeled edgelist |         ncol         |   Graph.Read_Ncol()    |   Graph.write_ncol()    |
|   Pajek format   |     pajek`, `net     |   Graph.Read_Pajek()   |   Graph.write_pajek()   |
|  Pickled graph   |        pickle        |  Graph.Read_Pickle()   |  Graph.write_pickle()   |

```python
karate = Graph.Read_GraphML("karate.GraphML")
karate.write_pajek("karate.net")
#从拓展名自主推断
karate = load("karate.GraphML")
karate.save("karate.net")
karate.save("karate.my_extension", format="gml")
####
g.Adjacency([[0,0,1,0],[1,0,0,0],[0,1,0,0],[1,1,1,1]],mode=ADJ_DIRECTED)
```





```python
import igraph

# 创建一个空对象
g = igraph.Graph()
# 添加网络中的点
vertex = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
g.add_vertices(vertex)
# 添加网络中的边
edges = [('a', 'c'), ('a', 'e'), ('a', 'b'), ('b', 'd'), ('b', 'g'), ('c', 'e'),
         ('d', 'f'), ('d', 'g'), ('e', 'f'), ('e', 'g'), ('f', 'g')]
g.add_edges(edges)
# -----------------------其它信息-----------------------------
# 国家名称
g.vs['label'] = ['齐', '楚', '燕', '韩', '赵', '魏', '秦']
# 国家大致相对面积（为方便显示没有采用真实面积）
g.vs['aera'] = [50, 100, 70, 40, 60, 40, 80]
# 统计日期
g['Date'] = '公元前279年'
# -----------------------简单作图-----------------------------
# 选择图的布局方式
layout = g.layout('kk')
# 用Igraph内置函数绘图
igraph.plot(g)
```

