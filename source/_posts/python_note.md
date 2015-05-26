title: python_note
date: 2015-04-23 12:48:01
tags: python
---

# python 笔记

## day 1 基本语法

## day 2 高级语法

### decorator 装饰器

装饰器是一个特殊的函数，它接受函数为参数，同时在它自己的内部定义一个函数(我们叫它包装函数Wrapper），在包装函数中做一些动作，同时调用函数的参数，然后返回这个包装函数。看上去挺难懂，其实看代码就比较容易了。

~~~python

def print_args(func,*args,**kargs):
	def wrapper(func,*args,**kargs):
		print("function {0} and its args {1} and {2}".format(func,*args,**kargs))
		func(*args,**kargs)
		print("goodbye")
	return wrapper
	
~~~

设想这样一个场景，我们自己写了很多函数

~~~python
def square(x):
    return x ** 2

def cubic(x):
    return x ** 3
~~~

但是这样的函数只能一个一个地算，不能接受列表作为参数的。我们当然不想一个函数一个函数得去改。
最好能加上个 listable 的功能就好了（类似 Mathematica 的 Listable Attribute)。

这个时候就轮到装饰器上场了。

~~~python
def listable(func):
    def wrapper(*args):
        arg_list = list(*args)
        results = [func(arg) for arg in arg_list]
        if len(results) == 0:
            return results[0]
        else:
            return results
    return wrapper
~~~

然后，我们可以给我们自己写得函数加上这个装饰器，这样就可以处理列表参数啦

~~~python
@listable
def square(x):
    return x ** 2
~~~

当然最让我震撼的是我看到下面的代码，它使用装饰器来缓存递归函数的值，可以让递归不用重复计算。
虽然可以改成迭代，但是递归代码往往更容易理解。而且，使用装饰器后，可以安心的写递归代码，其他都不用操心了。

~~~python

    def memoize(func):
        func.cache = {}
        def wrapper(*args):
            if args in func.cache:
                return func.cache[args]
            else:
                func.cache[args] = func(*args)
                return func.cache[args]
        return wrapper


    @memoize
    def fibonacci(n):
        if n <= 0:
            print("n must be positive integer")
        if n == 1 or n == 2:
            return 1
        else: return fibonacci(n-1)+fibonacci(n-2)
~~~

还有一个让我觉得牛逼的，就是 ipython notebook。 这家伙居然知道我用了缓存。

~~~python

    %timeit fibonacci(10)
~~~
    The slowest run took 50.45 times longer than the fastest. This could mean that an intermediate result is being cached 
    1000000 loops, best of 3: 276 ns per loop



## day 5 算法

### 快排

~~~python
def quicksort(alist):
    if len(alist)<=1:
        return alist
    else:
        pivot = alist.pop(np.random.randomint(len(alist)))
        lesser = [item for item in alist if item < pivot]
        greater = [item for item in alist if item >= pivot]
    return lesser + [pivot] + greater
~~~

### 二分搜索

~~~python
def search(sorted_list,element):
    #假定列表是升序排列
    def bsearch(sorted_list,element,low,high):
        if high == low:
            return element == sorted_list[high]
        mid = (high + low)//2
        if sorted_list[mid] == element:
            return True
        elif sorted_list[mid] > element:
            if mid == low:
                return False
            else:
                return bsearch(sorted_list,element,low,mid-1)
        else:
            return bsearch(sorted_list,element,mid+1,high)
        
        Length = len(sorted_list)
    if Length == 0:
        return False
    else:
        return bsearch(sorted_list,element,0,Length - 1)
~~~

### 深度优先搜索(DFS)

~~~python
graph = {'A': set(['B', 'C']),
         'B': set(['A', 'D', 'E']),
         'C': set(['A', 'F']),
         'D': set(['B']),
         'E': set(['B', 'F']),
         'F': set(['C', 'E'])}

def dfs(graph, start):
    visited, stack = set(), [start]
    while stack:
        vertex = stack.pop()
        if vertex not in visited:
            visited.add(vertex)
            stack.extend(graph[vertex] - visited)
    return visited

dfs(graph, 'A') # {'E', 'D', 'F', 'A', 'C', 'B'}
~~~