title: "fluent python note"
date: 2015-10-06 16:32:27
tags: python
---
下狠心在美亚上买了原版，不认真读读做笔记有些说不过去。


## 第一章 python 数据模型

为什么要了解数据模型？ 为了更好的写出 pythonic 的代码。

定义：数据模型是对 python 框架的描述，它形式化定义了一些接口，这些接口是 python 语言本身的一些结构单元，比如:sequences, iterators, functions, classes context managers, and so on.

作者从一个扑克牌说起.

~~~python
import collections
Card = collections.namedtuple('Card',['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2,11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]
    
    def __len__(self):
        return len(self._cards)
    
    def __getitem__(self, position):
        return self._cards[position]
~~~

代码写很优美有没有？ 虽然 FrenchDeck 类很短，但是它还是含有很多东西的。

~~~python
deck = FrenchDeck()
len(deck)
~~~


特别注意两个带有下滑先的函数.

首先，我们可以通过用 系统的 len 函数获得卡牌的长度。
如果我们想获得特定的某一张牌，比如第一张或者最后一张