title: "dijkstra 算法"
date: 2015-04-29 18:46:15
tags: [python,algorithms,Graph]
---

输入图 G，初始点src
输出：初始点到所有点得最短路径

思路：
1.初始化，用字典表示，将所有点放入字典distance,distance[src] = 0,
其余点设为 inf 。
distance[v] 表示src 到 v 的当前最短距离。
并设所有点为未访问过的 unvisited。

2.探索和 src 相邻的边，更新 src 相邻顶点 v 的值：
如果 distance[src]+weight[src][v] < distance[v] ,就更新 distance[v]
且更新 unvisited[v]的距离

3.将 src 的相邻边全部探索完毕后，
     将 src 设置为访问过的。（将 src 从 unvisited 中弹出）
    
4.从剩下的unvisited 中，选取最小的顶点作为新的 src
这里有个小技巧(python): src = min(unvisited，**key=unvisited.get**).

5.重复直到 unvisited 为空。

个人理解这里的关键是：**直到 src 的相邻边都探索完毕后，才将 src 设置为访问过的**

之前从 src 更新 distance[v] 的值的时候，就把 v 设置成访问过的，这样是错的，因为可能还有从其他点来更新 distance[v].

下面附上 wiki 伪代码和自己写得 python 代码

     1  function Dijkstra(Graph, source):
     2
     3      dist[source] ← 0                       // Distance from source to source
     4      prev[source] ← undefined               // Previous node in optimal path initialization
     5
     6      for each vertex v in Graph:  // Initialization
     7          if v ≠ source            // Where v has not yet been removed from Q (unvisited nodes)
     8              dist[v] ← infinity             // Unknown distance function from source to v
     9              prev[v] ← undefined            // Previous node in optimal path from source
    10          end if 
    11          add v to Q                     // All nodes initially in Q (unvisited nodes)
    12      end for
    13      
    14      while Q is not empty:
    15          u ← vertex in Q with min dist[u]  // Source node in first case
    16          remove u from Q 
    17          
    18          for each neighbor v of u:           // where v is still in Q.
    19              alt ← dist[u] + length(u, v)
    20              if alt < dist[v]:               // A shorter path to v has been found
    21                  dist[v] ← alt 
    22                  prev[v] ← u 
    23              end if
    24          end for
    25      end while
    26
    27      return dist[], prev[]
    28
    29  end function
    
    
~~~python
def dijkstra(g,src):
    distance = {}
    #predecessor = {}
    #visited = []
    unvisited = {}
    for vertex in g:
        distance[vertex] = float('inf')
        unvisited[vertex] = distance[vertex]

    distance[src] = 0
    unvisited[src] = 0
    #predecessor[src] = None

    while unvisited:
        #这里将 src 放在这个位置，而不是 unvisited.pop语句附近，是到最后的时候 unvisited 为空，从而会导致错误。
        src = min(unvisited,key = unvisited.get)
        for neighbor in g[src]:
            if neighbor not in visited:
                new_distance = distance[src] + g[src][neighbor]
                if new_distance < distance[neighbor]:
                    distance[neighbor] = new_distance
                    unvisited[neighbor] = new_distance

        #visited.append(src)
        unvisited.pop(src)
        predecessor[neighbor] = src




    return distance


if __name__ == "__main__":
    mygraph = {'s': {'a': 2, 'b': 1},
               'a': {'s': 3, 'b': 4, 'c': 8},
               'b': {'s': 4, 'a': 2, 'd': 2},
               'c': {'a': 2, 'd': 7, 't': 4},
               'd': {'b': 1, 'c': 11, 't': 5},
               't': {'c': 3, 'd': 5}}

    dist = dijkstra(mygraph,'s')

    print(dist)
~~~

wiki 里面有个用 priority queue 实现的，非常漂亮。不过我现在还没学 priority queue。