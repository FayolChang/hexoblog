title: "sympy tutorial"
date: 2015-05-03 16:56:13
tags: [python, sympy]
---

自从看了卡尔曼滤波的教程，看到里面用到了 sympy 来显示公式和进行公式推导，忍不住心里痒痒，就想学学该怎么用。


    from sympy import *
    from sympy.interactive import printing
    printing.init_printing(use_latex=True)

这里默认使用 ipython notebook，上面的语句会使用比较漂亮的方式来显示公式。

## 本文主要记录:
1.变量的定义

2.演算

* 极限
* 微分
* 级数展开
* 求和
* 积分
* 复数
* 函数
* 微分方程
* 代数方程
* 矩阵

    

## 变量的定义

变量的定义使用 symbols 函数，可以限制变量的类型，比如为整数或者为函数等。


    x, y, z, t = symbols('x y z t')


    i,k, m, n = symbols('i k m n', integer=True)


    f, g, h = symbols('f g h', cls=Function)


    symbols('x:10')




$$\left ( x_0, \quad x_1, \quad x_2, \quad x_3, \quad x_4, \quad x_5, \quad x_6, \quad x_7, \quad x_8, \quad x_9\right )$$



## 极限


    limit(sin(x)/x,x,0)



$$1$$



**大小写的区别是大写不运算，小写给出运算结果**


    Limit((3 * n ** 2 + 2 * n )/ (n ** 2 + 5),n,oo)




$$\lim_{n \to \infty}\left(\frac{3 n^{2} + 2 n}{n^{2} + 5}\right)$$




    limit((3 * n ** 2 + 2 * n )/ (n ** 2 + 5),n,oo)




$$3$$



## 微分


    diff(sin(x),x)




$$\cos{\left (x \right )}$$




    diff(sin(2*x), x, 2)




$$- 4 \sin{\left (2 x \right )}$$



## 积分


    Integral(sqrt(1/x),x)




$$\int \sqrt{\frac{1}{x}}\, dx$$




    integrate(sqrt(1/x),x)




$$2 x \sqrt{\frac{1}{x}}$$




    integrate(sqrt(1/x),(x,1,2))




$$-2 + 2 \sqrt{2}$$



## 多项式


    expand((x + 1)**2)




$$x^{2} + 2 x + 1$$




    factor(x**3 - x**2 + x - 1)




$$\left(x - 1\right) \left(x^{2} + 1\right)$$



## 级数展开

使用.series(var, point, order):


    (1/cos(x)).series(x, 0, 10)




$$1 + \frac{x^{2}}{2} + \frac{5 x^{4}}{24} + \frac{61 x^{6}}{720} + \frac{277 x^{8}}{8064} + \mathcal{O}\left(x^{10}\right)$$




    integrate(exp(-x ** 2),x).series(x,0,10)




$$x - \frac{x^{3}}{3} + \frac{x^{5}}{10} - \frac{x^{7}}{42} + \frac{x^{9}}{216} + \mathcal{O}\left(x^{10}\right)$$



### 画出级数图形


    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt
    def plot_taylor_approximations(func, x0=None, orders=(2, 4), xrange=(0,1), yrange=None, npts=200):
        """Plot the Taylor series approximations to a function at various orders.
    
        Parameters
        ----------
        func : a sympy function
        x0 : float
          Origin of the Taylor series expansion.  If not given, x0=xrange[0].
        orders : list
          List of integers with the orders of Taylor series to show.  Default is (2, 4).
        xrange : 2-tuple or array.
          Either an (xmin, xmax) tuple indicating the x range for the plot (default is (0, 1)),
          or the actual array of values to use.
        yrange : 2-tuple
          (ymin, ymax) tuple indicating the y range for the plot.  If not given,
          the full range of values will be automatically used. 
        npts : int
          Number of points to sample the x range with.  Default is 200.
        """
        if not callable(func):
            raise ValueError('func must be callable')
        if isinstance(xrange, (list, tuple)):
            x = np.linspace(float(xrange[0]), float(xrange[1]), npts)
        else:
            x = xrange
        if x0 is None: x0 = x[0]
        xs = Symbol('x')
        # Make a numpy-callable form of the original function for plotting
        fx = func(xs)
        f = lambdify(xs, fx, modules=['numpy'])
        # We could use latex(fx) instead of str(), but matploblib gets confused
        # with some of the (valid) latex constructs sympy emits.  So we play it safe.
        plt.plot(x, f(x), label=str(fx), lw=2)
        # Build the Taylor approximations, plotting as we go
        apps = {}
        for order in orders:
            app = fx.series(xs, x0, n=order).removeO()
            apps[order] = app
            # Must be careful here: if the approximation is a constant, we can't
            # blindly use lambdify as it won't do the right thing.  In that case, 
            # evaluate the number as a float and fill the y array with that value.
            if isinstance(app, numbers.Number):
                y = np.zeros_like(x)
                y.fill(app.evalf())
            else:
                fa = lambdify(xs, app, modules=['numpy'])
                y = fa(x)
            tex = latex(app).replace('$', '')
            plt.plot(x, y, label=r'$n=%s:\, %s$' % (order, tex) )
            
        # Plot refinements
        if yrange is not None:
            plt.ylim(*yrange)
        plt.grid()
        plt.legend(loc='best').get_frame().set_alpha(0.8)


    # You can change the default figure size to be a bit larger if you want,
    # uncomment the next line for that:
    #plt.rc('figure', figsize=(10, 6))
    plt.rc('figure', figsize=(15, 9))
    plot_taylor_approximations(sin, 0, [2, 4, 6, 8, 10, 15, 20], (0, 2*pi), (-2,2))


{% asset_img sympy-tutorial_30_0.png %}


## 求和


    Sum(i ** 2,(i,1,n))




$$\sum_{i=1}^{n} i^{2}$$




    Sum(i ** 2,(i,1,n)).doit()




$$\frac{n^{3}}{3} + \frac{n^{2}}{2} + \frac{n}{6}$$



## 复数


    exp(I*x).expand()




$$e^{i x}$$




    exp(I*x).expand(complex = True)




$$i e^{- \Im{x}} \sin{\left (\Re{x} \right )} + e^{- \Im{x}} \cos{\left (\Re{x} \right )}$$



## 微分方程


    f(x).diff(x, x) + f(x)




$$f{\left (x \right )} + \frac{d^{2}}{d x^{2}}  f{\left (x \right )}$$




    dsolve(f(x).diff(x, x) + f(x), f(x))




$$
f(x) = C_1 \sin(x) + C_2 \cos(x)
$$



## 矩阵


    A = Matrix([[x ** 2,2*x*y], [2*x*y,y ** 2]])


    A




$$\left[\begin{matrix}x^{2} & 2 x y\\\2 x y & y^{2}\end{matrix}\right]$$




    A ** 2




$$\left[\begin{matrix}x^{4} + 4 x^{2} y^{2} & 2 x^{3} y + 2 x y^{3}\\\2 x^{3} y + 2 x y^{3} & 4 x^{2} y^{2} + y^{4}\end{matrix}\right]$$




    variables = Matrix([x,y])


    (A ** 2).row(0)




$$\left[\begin{matrix}x^{4} + 4 x^{2} y^{2} & 2 x^{3} y + 2 x y^{3}\end{matrix}\right]$$




    (A ** 2).row(0).jacobian(variables)




$$\left[\begin{matrix}4 x^{3} + 8 x y^{2} & 8 x^{2} y\\\6 x^{2} y + 2 y^{3} & 2 x^{3} + 6 x y^{2}\end{matrix}\right]$$




    hessian(f(x,y),(x,y))




$$\left[\begin{matrix}\frac{\partial^{2}}{\partial x^{2}}  f{\left (x,y \right )} & \frac{\partial^{2}}{\partial x\partial y}  f{\left (x,y \right )}\\\
\frac{\partial^{2}}{\partial x\partial y}  f{\left (x,y \right )} & \frac{\partial^{2}}{\partial y^{2}}  f{\left (x,y \right )}\end{matrix}\right]$$




    diff(f(x,y),x)




$$\frac{\partial}{\partial x} f{\left (x,y \right )}$$




    
