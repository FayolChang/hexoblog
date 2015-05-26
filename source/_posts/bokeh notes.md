# bokeh 笔记

> The basic steps to creating plots with the bokeh.plotting interface are:
1.Prepare some data (in this case plain python lists).
2.Tell Bokeh where to generate output (in this case using output_file(), with "lines.html" as the filename to save as).
3.Call figure() to create a plot with some overall options like title, tools and axes labels.
4.Add renderers (in this case, Figure.line) for our data, with visual customizations like colors, legends and widths to the plot.
5.Ask Bokeh to show() or save() the results.

## the bokeh.plotting Interface

### 生成数据
可以是 list, array? DataFrame(pandas)?

### 指定输出
可以是一个 html 文件或者是 ipython notebook


### 生成一个 figure
这一步使用 figure 函数 生成一个图，在这一步可以设定图形的一些参数：
* title
* plot\_width,plot\_height
* x\_axis\_label,y\_axis\_label
* x\_range , y\_range 可以是2个元素的列表或者元组:[1,10] or (1,10)
* tools,可以从这里面随便选TOOLS="resize,crosshair,pan,wheel_zoom,box_zoom,reset,box_select,lasso_select"
* x\_axis\_type,y\_axis\_type
* legend
* toolbar_location:  could be  "above" "below" "left","right"

### 利用数据绘图
在这一步才是利用数据绘图。
bokeh provides several functions to draw different shape of glyphs.

* 'annular_wedge',
* 'annulus',
* 'arc',
* 'asterisk',
* 'bezier',
* 'circle',
* 'circle_cross',
* 'circle_x',
* 'cross',
* 'diamond',
* 'diamond_cross',
* 'image',
* 'image_rgba',
* 'image_url',
* 'inverted_triangle',
* 'line',
* 'multi_line',
* 'oval',
* 'patch',
* 'patches',
* ...

more than I can image. each glyph has its own parameters. these functions return  a `plot`

### show the plot
Finally, `show` function shows the plot.

## bokeh.charts Interface

In this Interface, there are several functions:
~~~
    'Area',
    'Bar',
    'BoxPlot',
    'ColumnDataSource',
    'DataAdapter',
     'Donut',
     'Dot',
     'HBox',
     'HeatMap',
     'Histogram',
     'Horizon',
     'Line',
     'Scatter',
     'Step',
     'TimeSeries',
     'VBox',
 ~~~