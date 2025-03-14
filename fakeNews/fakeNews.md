# 方法一：单平台/跨平台传播路径方式

初步技术选型：图神经网络（GNN）；

训练数据集格式（有角色标注，即源头、核心、桥梁）：

节点数据：
```json
[
  {"node_id": "U001", "type": "user", "platform": "Twitter", "features": {"followers": 5000, "following": 200, "posts": 50}, "label": "source"},
  {"node_id": "U002", "type": "user", "platform": "Twitter", "features": {"followers": 3000, "following": 500, "posts": 30}, "label": "core"},
  {"node_id": "U003", "type": "user", "platform": "Facebook", "features": {"followers": 2000, "following": 600, "posts": 25}, "label": "bridge"},
  {"node_id": "U004", "type": "user", "platform": "Facebook", "features": {"followers": 1500, "following": 400, "posts": 20}, "label": "core"},
  {"node_id": "U005", "type": "user", "platform": "Reddit", "features": {"followers": 1000, "following": 700, "posts": 15}, "label": "bridge"},
  {"node_id": "P001", "type": "post", "platform": "Twitter", "features": {"likes": 400, "shares": 200, "timestamp": "2025-03-13T08:00:00Z"}},
  {"node_id": "P002", "type": "post", "platform": "Facebook", "features": {"likes": 300, "shares": 100, "timestamp": "2025-03-13T09:00:00Z"}}
]

```
