title: "DFS and BFS"
date: 2015-04-24 12:20:47
tags: [python,algorithms,graph]
---


今天学习了树深度优先遍历和广度优先遍历，对我自己来说，还是邻接表 (adjacent List) 比较符合自己的思维。下面是算法用得图形

~~~python
    from IPython.display import Image
    Image(url='http://glue.jjj.sun.ac.za/~james/IntroPython/images/binary.png')

~~~


<img src="http://glue.jjj.sun.ac.za/~james/IntroPython/images/binary.png"/>



这个图用邻接表表示就如程序中的 graph 变量。
这里巧妙地用了列表来存储节点。注意广度优先遍历和深度优先遍历只差一个：
**是先弹出(pop)最后一个还是第一个**。

~~~python
    graph={ 4:[2,6], 2:[4,1,3], 6:[4,5,7], 3:[2], 1:[2], 5:[6], 7:[6] }
    
    def dfs(graph, start):
        path = []
        lst = [start]
        while lst:
            vertex = lst.pop(-1)
            if vertex not in path:
                path.append(vertex)
                lst.extend(graph[vertex])
        return path
    
    print dfs(graph, 4)

    [4, 6, 7, 5, 2, 3, 1]
~~~

~~~python
    #graph={ 4:[2,6], 2:[4,1,3], 6:[4,5,7], 3:[2], 1:[2], 5:[6], 7:[6] }
    
    def bfs(graph, start):
        path = []
        queue = [start]
        while queue:
            vertex = queue.pop(0)
            if vertex not in path:
                path.append(vertex)
                queue.extend(graph[vertex])
        return path
    
    print bfs(graph, 4)

    [4, 2, 6, 1, 3, 5, 7]
~~~

一开始我没有看明白，就一点一点来

~~~python
   lst = [4]
~~~

~~~python
    lst
~~~

~~~python
    [4]
~~~

~~~python
    lst.pop(-1)
~~~

~~~python
   4
~~~

~~~python
   lst
~~~

~~~python
    []
~~~

~~~python
   lst.extend([2,6])
    lst
~~~

~~~python
   [2, 6]
~~~



这里是关键了，如果一直是 pop(-1)，那么后加进来的就会被先弹出

~~~python
    lst.pop(-1)
~~~


~~~python
    6
~~~

~~~python
   lst
~~~

~~~python
    [2]
~~~

~~~python
   lst.extend([5,7])
   lst
~~~

~~~python
   [2, 5, 7]
~~~

到这里已经很清楚了，如果是弹出第一个，那么节点2会被先弹出，而不是7这样就是广度优先，如果先弹出最后一个节点，这里是7，就是深度优先。
