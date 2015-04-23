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

## day 5 算法

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