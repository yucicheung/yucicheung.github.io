---
title: Dijkstras算法和实现(python)
tags:
  - 数据结构
  - 算法
  - python
categories:
  - 学艺更精
date: 2018-03-24 01:37:48
---

<Excerpt in index | 首页摘要> 
- Dijkstras算法的基本原理和求解步骤
- Dijkstras算法用python实现的思路和源代码
- Dijkstras算法的适用范围
代码和笔记记录下载请到[我的github仓库](https://github.com/yucicheung/grokking_algorithms_practice/tree/master/07_dijkstras_algorithm)。
<!-- more -->
<The rest of contents | 余下全文>
## 基本原理

1. 广度优先搜索，适用于*非加权图*(unweighted graph)，找到的*最短路径*是段数最少的路径。**迪克斯特拉**(Dijkstras)算法，适用于*加权图*(weighted graph)，找出的是总权重最小的路径。
2. Dijsktra算法包含四个步骤：
   1. 找出当前离起点最近的节点；
   2. 对于该节点的邻居，检查是否有前往它们的更短路径，如果有，就更新其开销，并更新邻居的父节点；
   3. 重复这个过程，直到对所有节点都这样做了；
   4. 计算最终路径(路线和开销)。
3. Dijstras的基本思想：对于处理的节点，已经找到到它们的最短路径(或没有到达它们的更短路径)。
4. 以下图题目为例:
   ![dijkstras_init](/img/dijkstras_init.jpg)

   利用算法求最短路径的求解过程如下：

   ![dijkstras_solve](/img/dijkstras_solve.jpg)

   于是最终得到的最短路径`start--> B--> A--> fin`,开销为6。

## 算法实现
1. 节点的开销应初始化为无穷大,保证只要有路线可达，其开销一定能被更新。Python中是`float('inf')`。
2. 加权有向图仍然是散列表实现。
3. 求解全过程要用到：
   - 有向图散列表；
   - 开销散列表;
   - 父节点散列表;
   - 待处理节点列表(或已处理节点列表)。
4. 对`fin`终点不需要进行Dijkstras算法。

## 适用范围
1. 首先，Dijkstras算法适用于有向加权图。
2. 对于*有向无环图*(directed acyclic graph, DAG)也是适用的，因为Dijkstras算法会自动淘汰含有环的边。
3. **不适用**含负权边的有向图，负权边会在节点检查过之后仍然更新其开销，不符合Dijkstras算法的基本思想。在包含负权边的图中找最短路径要用另一种算法——Bellman-Ford算法。

## 代码实现(python)
针对上面的有向图求解。
代码实现和笔记都在[我的github仓库](https://github.com/yucicheung/grokking_algorithms_practice/tree/master/07_dijkstras_algorithm)可以下载到。

```python
from pprint import pprint


def find_lowest_cost(costs,to_process):
	lowest_cost_node = to_process[0]
	lowest_cost = costs[lowest_cost_node]
	if len(to_process)>1:
		for node in to_process[1:]:
			new_cost = costs[node]
			if new_cost < lowest_cost:
				lowest_cost = new_cost
				lowest_cost_node = node
	return lowest_cost_node


def set_up_graph():
	graph = {}
	graph['start'] = {"A":6,"B":2}
	graph["A"] = {'fin':1}
	graph["B"] = {"A":3,"fin":5}
	graph['fin'] = {}
	return graph


def initialize_costs_n_fathers(graph):
	costs, fathers ={},{}
	for node in graph:
		if node is "start":
			costs[node] = 0
			fathers[node] = None
		else:
			costs[node] = float('inf')
	return costs,fathers


def get_shortest_path(fathers):
	path = []
	father = 'fin'
	path.append(father)
	while father != 'start':
		father = fathers[father]
		path.append(father)
	print 'Shortest path is:',
	for i in path[-1:-len(path):-1]:
		print '{}-->'.format(i),
	print path[0]


def main():
	graph = set_up_graph()
	print 'Graph as below:'
	pprint(graph)
	print '\n'
	costs, fathers = initialize_costs_n_fathers(graph)
	to_process = [i for i in graph.keys()]
	to_process.remove('fin')
	while to_process:
		node = find_lowest_cost(costs,to_process)
		neighbors = graph[node]
		for neighbor in neighbors:
			new_cost = costs[node] + graph[node][neighbor]
			if new_cost < costs[neighbor]:
				costs[neighbor] = new_cost
				fathers[neighbor] = node
		to_process.remove(node) # keys donnot share names
	get_shortest_path(fathers)
	print 'The lowest cost is {}.'.format(costs['fin'])
	
	
if __name__=='__main__':
	main()
```
运行结果为：
```bash
Graph as below:
{'A': {'fin': 1},
 'B': {'A': 3, 'fin': 5},
 'fin': {},
 'start': {'A': 6, 'B': 2}}


Shortest path is: start--> B--> A--> fin
The lowest cost is 6.
```
也可以将上面的图改成是有环的图，就能验证到对有环图也是适用的。


















