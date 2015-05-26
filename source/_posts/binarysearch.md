title: "binary search"
date: 2015-04-27 17:31:54
tags: [python, algorithms, search]
---
二分搜索看似简单，其实还是有点细节需要注意的。据说第一篇二分搜索的论文在1946年提出，但是第一个没有错误的实现要到1962年才出现（来自编程珠玑p34）。只有10%的程序员写对了。

二分搜索思想很简单，对于一个已经有序的序列 mylist，我们设 start = 0, end = len(mylist)-1.
我们找到序列中间的部分 mid, 如果要查找的值value正好就是 mid 位置的值就返回，否则，如果 value 小于序列中值，则在 start 到 mid-1 之间查找。反之，则查找 mid+1 到 end 部分。 （递归地查找）

需要注意的地方是什么时候程序结束。

由于每次查找，序列都会缩短一半，我们先看看最后剩3个元素的情况。
start mid end

这时候，如果 value 的值在 mylist[start] 和 mylist[mid]中间，这时，该搜索 start 和 mid-1 位置，也就是只剩1个元素了。
这个时候如果相等就返回 True，不相等就返回 False。

如果最后还剩4个元素：

start pos1 pos2 end

那么中间位置应该是 item1,所以我们只要考虑2个元素的情况了。

start end

此时的 mid=start

所以此时如果 value 小于 mylist[mid] 的话，只能返回 False 了。

总结：
** 1.如果只剩1个元素，即 start = end,直接比较然后返回结果 **
** 2.如果剩2个元素， 此时start = mid,如果 value 还是小于 mylist[mid]则返回 False **

我好像漏了没有考虑 value 大于 mylist[mid]的情况。其实如果大于的话，直接在 mid+1 和 end 位置找，就回到
$ mylist[mid+1] \leq value mylist[end] $ 的情况了。

~~~python
def bsearch(alist,value):
    #alist is sorted,increasing
    if value < alist[0] or value > alist[-1]:
        return False
    
    def _bsearch(alist,value,start,end):
        if start == end and alist[start] != value:
            return False
        
        mid = (start + end)//2
        
        if alist[mid] == value:
            return True
        elif value < alist[mid] :
            if start == mid:
                return False
            else:
                return _bsearch(alist,value,start,mid-1)
        else:
            return _bsearch(alist,value,mid+1,end)
    return _bsearch(alist,value,0,len(alist))
        
~~~

~~~python
    #test
for num in range(min(random_list),max(random_list)+1):
    result = bsearch(random_list,num)
    num_in_list = num in random_list
    assert result == num_in_list
~~~