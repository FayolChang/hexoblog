title: "kruskal"
date: 2015-04-27 12:20:47
tags: [python,algorithms,graph]
---

kruskal 算法是一种贪心算法。基本思想是：
1.先把原图 G 的所有顶点(vertex)加到一个集合。把这些顶点看成一棵棵单独的树，每个树的根指向自身。
2.对G 的所有边进行从小到大排序。
3.把这些边一条条的加入新的集合。注意不要产生回路(环）。直到所有边处理完毕或者边的数目达到 V - 1

伪代码如下（来自 wiki）

    KRUSKAL(G):
    1 A = ∅
    2 foreach v ∈ G.V:
    3    MAKE-SET(v)
    4 foreach (u, v) ordered by weight(u, v), increasing:
    5    if FIND-SET(u) ≠ FIND-SET(v):
    6       A = A ∪ {(u, v)}
    7       UNION(u, v)
    8 return A

Iamge   | Description
------- | -------------- 
![1](figures/kruskal/400px-Kruskal_Algorithm_1.png )| **AD** and **CE** are the shortest edges, with length 5, and **AD** has been arbitrarily chosen, so it is highlighted.
![2](figures/kruskal/400px-Kruskal_Algorithm_2.png)| **CE** is now the shortest edge that does not form a cycle, with length 5, so it is highlighted as the second edge.
![3](figures/kruskal/400px-Kruskal_Algorithm_3.png)| The next edge, **DF** with length 6, is highlighted using much the same method.
![4](figures/kruskal/400px-Kruskal_Algorithm_4.png)| The next-shortest edges are **AB** and **BE**, both with length 7. **AB** is chosen arbitrarily, and is highlighted. The edge **BD** has been highlighted in red, because there already exists a path (in green) between **B** and **D**, so it would form a cycle (**ABD**) if it were chosen.
![5](figures/kruskal/400px-Kruskal_Algorithm_5.png)| The process continues to highlight the next-smallest edge, **BE** with length 7. Many more edges are highlighted in red at this stage: **BC** because it would form the loop **BCE**, **DE** because it would form the loop **DEBA**, and **FE** because it would form **FEBAD**.
![6](figures/kruskal/400px-Kruskal_Algorithm_6.png)| Finally, the process finishes with the edge **EG** of length 9, and the minimum spanning tree is found.


这里面最引人入胜的是引入 disjoint set 来避免回路。注意到当加入边 e 时，如果 e 的2个邻接点在同一棵树中，那么必然产生回路。

所以我们在加入边的时候，要判断2个邻接点是不是在同一棵树中。如何判断呢？我们可以找出其根节点，如果2个节点的根节点相同，我们就不加入这条边。



~~~python
class DisjointSet(dict):

    ##字典的 value存的是 root 
    def add(self, item):
        self[item] = item
 
    def find(self, item):
        parent = self[item]
 
        while self[parent] != parent:
            parent = self[parent]
 
        self[item] = parent
        return parent
        
    #正常情况下，是应该找到 item1，item2的 root 的，但是在方法 kruskal 中，t1,t2
    #已经是 root 了，故不需要在找了
    def union(self, item1, item2):
        self[item2] = self[item1]



def kruskal( nodes, edges ):
    forest = DisjointSet()
    mst = []
    for n in nodes:
        forest.add( n )
 
    sz = len(nodes) - 1
 
    for e in sorted( edges, key=lambda edge:edge[2] ):
        n1, n2, _ = e
        t1 = forest.find(n1)
        t2 = forest.find(n2)
        if t1 != t2:
            mst.append(e)
            sz -= 1
            if sz == 0:
                return mst
         
            forest.union(t1, t2)
 
 
#test   
 
nodes = list( "ABCDEFG" )
edges = [ ("A", "B", 7), ("A", "D", 5),
          ("B", "C", 8), ("B", "D", 9), ("B", "E", 7),
      ("C", "E", 5),
      ("D", "E", 15), ("D", "F", 6),
      ("E", "F", 8), ("E", "G", 9),
      ("F", "G", 11)]
         
print ( kruskal( nodes, edges ) )
#output: [('A', 'D', 5), ('C', 'E', 5), ('D', 'F', 6), ('A', 'B', 7), ('B', 'E', 7), ('E', 'G', 9)]
~~~