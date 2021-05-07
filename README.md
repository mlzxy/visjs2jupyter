# Visualization Tool for Image Clustering

modified from https://github.com/ucsd-ccbb/visJS2jupyter


## Installation

```bash
python3 -m pip install git+https://github.com/mlzxy/visjs2jupyter@main --user
```

And install https://chrome.google.com/webstore/detail/ignore-x-frame-headers/gleekbfjekiniecknbkamfmkohkpodhe , to make the browser support CORS request. 


## Example

[notebook](./example.ipynb)

Import necessary libraries

```python
from importlib import reload
import matplotlib as mpl
import networkx as nx
import visJS2jupyter.visJS_module as visJS_module
from tqdm.auto import tqdm
import random
reload(visJS_module)
```


generate a graph with 30 nodes

```python
# create a simple graph
G = nx.connected_watts_strogatz_graph(30,2,.2)
nodes = list(G.nodes()) # type cast to list in order to make compatible with networkx 1.11 and 2.0
edges = list(G.edges()) # for nx 2.0, returns an "EdgeView" object rather than an iterable
```


generate layout and set `node_image` for each node

```python
degree = dict(G.degree())
nx.set_node_attributes(G, name = 'degree', values = degree)
pos = nx.spring_layout(G)

hacker = 'base64 image string' 
humpy = 'or image url'
bnerd = ''
# set per-node attributes
nodes_dict = [{"id":n,
               "border_width": 4,
               "border_color": "#000",
               "degree": 5,
               "node_shape": 'image', # must set node shape to "image"
               "x":pos[n][0]*700,
                'node_image': random.choice([hacker, humpy, bnerd]),
               "y":pos[n][1]*700} for n in tqdm(nodes)
              ]
```


generate edges


```python
node_map = dict(zip(nodes,range(len(nodes))))

# set per-edge attributes
edges_dict = [{"source":node_map[edges[i][0]], "target":node_map[edges[i][1]], "id": str(random.random()),
              "color":"gray"} for i in range(len(edges))]
```

visualization

```python
data = visJS_module.visjs_network(nodes_dict,edges_dict,
                          node_size_multiplier=10,
                          node_size_transform = '',
                          node_shape_use_border_with_image=True,
                          node_font_size=25,
                          physics_enabled=True,
                          edge_color_highlight='#8A324E',
                          edge_color_hover='#8BADD3',
                          edge_width=3,
                          max_velocity=15,
                          min_velocity=1)
```



![](docs/visJS2Jupyter.png)

