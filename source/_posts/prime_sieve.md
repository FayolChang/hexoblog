title: "prime_sieve"
date: 2015-04-23 14:00:01
tags: 
    - python
    - prime
---

# 用筛法求N内素数和

起源于知乎上的一个问题，求10亿内的素数之和，什么方法最快？

下面给出的算法不是最快的，但是容易理解


~~~python
    import numpy
    
    def sieve6(n):
        """Return an array of the primes below n."""
        prime = numpy.ones(n, dtype=numpy.bool)
        prime[:2] = False
        prime[4::2] = False
        sqrt_n = int(n ** .5) + 1
        for p in range(3, sqrt_n, 2):
            if prime[p]:
                prime[p*p::2*p] = False
        return prime.nonzero()[0]
~~~

要点是：
* 从3开始，到 $\sqrt{n}$ ,步长为2,这样就把偶数给省去了。
* 在筛的过程中，从 $p^2$ 开始，步长是$2p$，而且用数组，可以一步就设定这么多数值

We could avoid allocating space for the even numbers, improving memory locality:

~~~python
    def sieve7(n):
        """Return an array of the primes below n."""
        prime = numpy.ones(n // 2, dtype=numpy.bool)
        sqrt_n = int(n ** .5) + 1
        for p in range(3, sqrt_n, 2):
            if prime[p // 2]:
                prime[p*p // 2::p] = False
        result = 2 * prime.nonzero()[0] + 1
        result[0] = 2
        return result
~~~
最快的方法，还是 Robert William Hanks 的：

~~~python
    def sieve8(n):
        """Return an array of the primes below n."""
        prime = numpy.ones(n//3 + (n%6==2), dtype=numpy.bool)
        for i in range(3, int(n**.5) + 1, 3):
            if prime[i // 3]:
                p = (i + 1) | 1
                prime[       p*p//3     ::2*p] = False
                prime[p*(p-2*(i&1)+4)//3::2*p] = False
        result = (3 * prime.nonzero()[0] + 1) | 1
        result[0] = 3
        return numpy.r_[2,result]
~~~


    
