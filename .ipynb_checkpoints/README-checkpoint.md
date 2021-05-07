# 簇可视化工具

modified from https://github.com/ucsd-ccbb/visJS2jupyter

帮助脚本 https://git-core.megvii-inc.com/snippets/324 （把这个脚本放入 jupyter notebook 运行，然后调用其中 `display_graph` 接口)

## 安装

```bash
python3 -m pip install git+ssh://git@git-core.megvii-inc.com/fdir/visjs2jupyter@master --user
```

同时安装 https://chrome.google.com/webstore/detail/ignore-x-frame-headers/gleekbfjekiniecknbkamfmkohkpodhe , 让浏览器支持外部资源跨域访问


## 使用例子

[notebook](./可视化簇例子.ipynb)

引用需要的库

```python
from importlib import reload
import matplotlib as mpl
import networkx as nx
import visJS2jupyter.visJS_module as visJS_module
from tqdm.auto import tqdm
import random
reload(visJS_module)
```


生成一个100节点的图

```python
# create a simple graph
G = nx.connected_watts_strogatz_graph(100,2,.2)
nodes = list(G.nodes()) # type cast to list in order to make compatible with networkx 1.11 and 2.0
edges = list(G.edges()) # for nx 2.0, returns an "EdgeView" object rather than an iterable
```


生成图的 layout，并设置每个节点的图片 `node_image`

```python
degree = dict(G.degree())
nx.set_node_attributes(G, name = 'degree', values = degree)

pos = nx.spring_layout(G)

nodes_dict = [{"id":n,
               "degree":nx.degree(G,n),
               "node_shape": 'image', # must set node shape to "image"
               "x":pos[n][0]*700,
                'node_image': "https://nv.iap.wh-a.brainpp.cn/erjin/1422884,11b3f80006c6aceda?longest=80",
               "y":pos[n][1]*700} for n in tqdm(nodes)
              ]
```


生成边，并随机设置边的权重


```python
node_map = dict(zip(nodes,range(len(nodes))))

# set per-edge attributes
edges_dict = [{"source":node_map[edges[i][0]], "target":node_map[edges[i][1]], "id": str(random.random()),
              "color":"gray"} for i in range(len(edges))]
```

进行可视化

```python
visJS_module.visjs_network(nodes_dict,edges_dict,
                          node_size_multiplier=10,
                          node_size_transform = '',
                          node_font_size=25,
                          physics_enabled=True,
                          edge_color_highlight='#8A324E',
                          edge_color_hover='#8BADD3',
                          edge_width=3,
                          max_velocity=15,
                          min_velocity=1)
```


![](./docs/graph-1.jpg)

还可以进行放大和拖拽

![](./docs/graph-2.jpg)
