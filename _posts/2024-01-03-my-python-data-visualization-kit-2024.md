---
layout: post
title: 'Python 数据可视化工具集 2024'
date: 2024-01-03 01:20 +0800
---


借用 [R4DS](https://r4ds.had.co.nz/explore-intro.html) 一书中对数据探索工作的描述，主要包括三个关键部件：**数据转换**、**可视化图表**、**数学建模**。一直以来，在 EDA 场景中，我自己使用 R & Python 的强度是五五开，甚至略微更偏向 R 一些。因为 R 宇宙有 [Tidyverse](https://tidyverse.org/)、[data.table](https://rdatatable.gitlab.io/data.table/) 这些工具集，都支持管道，且语法简明、逻辑优雅，借助 RStudio 和 RMarkdown，数据分析效率很高。而 Python + Jupyter + Pandas 这边，往往操作稍显罗嗦，组织主题更加麻烦一些。

进入 2023 年之后，Python 的使用比重就大的多了，主要原因有两个：一是因为 [Polars](https://pola.rs/)；二是因为 [Quarto](https://quarto.org/)。Polars 日臻成熟和流行，它兼顾了语义的优雅和性能的高效，新引入的抽象概念和 API 带来的「心智负担」也不算高，无论是从 Pandas 还是 Tidyverse 过渡而来，都非常容易。对于喜欢 R 管道的朋友，Polars 提供的链式 API，更是帮我们在 Python 环境里找到了家。再说 Quarto，RStudio/Posit 可能 All in Quarto，期望通过 Quarto 来包含 R/Python 的一切，来对抗 Jupyter。然时至今日，如果用户不进入 RStudio App，去手动链接各种 dart-sass、deno、esbuild，在苹果 M 系列芯片上，`.qmd` 文件还是跑不起来，勉强跑起来了，不知不觉又切回了更为熟悉轻量的 Rmd。加之今天又看到 [Yihui Down](https://yihui.org/en/2024/01/bye-rstudio/)，相当难过。Anyway，Good luck and have fun.

我几乎已经完全用 Polars 替换掉了 Pandas 和 Tidyverse。唯有两点遗憾，一是 Polars 没有内置的图表接口，二是还不能无缝关联模型库。不过，这两点都可以通过调用比如 `.to_pandas()`，将 DataFrame 转换成相应的兼容数据形式之后，再进行处理。后者，应该还需要不少的时间，而至于前一个遗憾，在最近几天发布的 `0.20.3` 版中解决了。现在 Polars 也内置了类似 Pandas `df.plot` 方式的绘图接口 🔥，必须赶紧体验一下，并且跟 Python 生态里我常用的绘图库做个对比，做个取舍。

以上篇[我在 YouTube 上看了些啥](https://placeless.net/blog/my-footprint-on-youtube)的数据集为例，做两件事，一是分别使用各个可视化框架，分别制作每月每个时段的热力图（heatmap）和每年的内容分类排名图（bump chart）；二是将两附图合在一起，探索一下各框架的多图布局功能。


```python
import polars as pl

df = pl.read_parquet("./history_aio.parquet")
pl.__version__
```




    '0.20.4'




```python
heatmap_dat = (
    # 挑出 2023 年
    df.filter(pl.col("year") == 2023)
    # 按月、小时段，做 count
    .group_by("month", "hour")
    .agg(
        pl.count().alias("n"),
    )
    # 透视拉宽、补列，完整 24 小时
    .pivot(index="month", columns="hour", values="n")
    .with_columns(
        pl.lit(None).alias("4"), pl.lit(None).alias("5"), pl.lit(None).alias("6")
    )
    .select(["month"] + [str(c) for c in range(1, 24)])
    # 融化还原成长表
    .melt(id_vars="month", variable_name="hour", value_name="n")
    # 变换类型并排序
    .with_columns(pl.col("hour").cast(pl.Int8))
    .sort("month", "hour")
)
```


```python
heatmap_dat.head()
```




<div><style>
.dataframe > thead > tr,
.dataframe > tbody > tr {
  text-align: right;
  white-space: pre-wrap;
}
</style>
<small>shape: (5, 3)</small><table border="1" class="dataframe"><thead><tr><th>month</th><th>hour</th><th>n</th></tr><tr><td>i8</td><td>i8</td><td>u32</td></tr></thead><tbody><tr><td>1</td><td>1</td><td>17</td></tr><tr><td>1</td><td>2</td><td>1</td></tr><tr><td>1</td><td>3</td><td>null</td></tr><tr><td>1</td><td>4</td><td>null</td></tr><tr><td>1</td><td>5</td><td>null</td></tr></tbody></table></div>




```python
bump_dat = (
    # 挑出有效类别行
    df.filter(pl.col("cat_name").is_not_null())
    # 按年、类，count 视频唯一量
    .group_by("year", "cat_name").agg(pl.n_unique("title").alias("n_video"))
    # 按量大小，排名
    .with_columns(
        pl.col("n_video").rank("ordinal", descending=True).over("year").alias("ranking")
    )
    # 按年排序
    .sort("year")
)
```


```python
bump_dat.head()
```




<div><style>
.dataframe > thead > tr,
.dataframe > tbody > tr {
  text-align: right;
  white-space: pre-wrap;
}
</style>
<small>shape: (5, 4)</small><table border="1" class="dataframe"><thead><tr><th>year</th><th>cat_name</th><th>n_video</th><th>ranking</th></tr><tr><td>i32</td><td>str</td><td>u32</td><td>u32</td></tr></thead><tbody><tr><td>2016</td><td>&quot;People &amp; Blogs…</td><td>44</td><td>4</td></tr><tr><td>2016</td><td>&quot;Sports&quot;</td><td>14</td><td>9</td></tr><tr><td>2016</td><td>&quot;Howto &amp; Style&quot;</td><td>41</td><td>5</td></tr><tr><td>2016</td><td>&quot;Autos &amp; Vehicl…</td><td>3</td><td>13</td></tr><tr><td>2016</td><td>&quot;Film &amp; Animati…</td><td>25</td><td>7</td></tr></tbody></table></div>



## Polars & hvPlot

Polars 并没有自行实现绘图逻辑，它的 `.plot` 是通过的 [hvPlot](https://hvplot.holoviz.org/index.html) 来代理的（而 hvPlot 默认的后端又是 [bokeh](https://bokeh.org/)，所以如果在 Notebook 环境中图表未正确显示，可以尝试使用以下方法正确显示图表）。


```python
# from bokeh.io import output_notebook

# output_notebook()
```


```python
fig1 = heatmap_dat.plot.heatmap(
    x="hour", y="month", C="n", cmap="reds", title="Heatmap"
).options(toolbar=None, default_tools=[])

fig1
```




<iframe id="fig1" frameborder=0 height="500px" width="100%" src="/files/charts/fig1.html"></iframe>



热力图是 hvPlot 预置的模板图表，所以一行代码，简单映射一下 xy 轴，再指定一下颜色，即可实现相当不错的效果。对于 bokeh 后端，如果不想看见它的工具栏，不喜欢它默认启用的缩放效果，我们也只需要在 `.plot` 之后跟 `.opts` 或者 `.options` 把它们关掉即可。

排名变化图 Bump Chart 并非 hvPlot 预置模板，需要自行实现。我们可以将其分解为散点图和条线图的结合体，分作制作这两者，然后将其合二为一。


```python
fig2 = (
    bump_dat.plot.line(x="year", y="ranking", by="cat_name")
    * bump_dat.plot.scatter(x="year", y="ranking", by="cat_name")
).opts(
    title="Bump Chart",
    toolbar=None,
    default_tools=[],
    invert_yaxis=True,
    legend_opts=dict(border_line_width=0, label_text_font_size="6pt", label_height=2),
    padding=0.05,
    height=480,
)

fig2
```






<iframe id="fig2" frameborder=0 height="500px" width="100%" src="/files/charts/fig2.html"></iframe>



微调的图表参数，可以大胆假设，先填进去，脚本报错之后，框架根据你的填写，会贴心的爆出它的合理推测或支持的参数集，如此两三个来回，再结合文档，实现想要的图表效果，门槛也不算太高。上图比较打眼的部分是右边的图例（legend）栏，它的排序没有能跟离它最近的 2023 年排名保持一致，观感不好。我们可以单独对它进行重排，或者拿掉它之后单独对 2023 的排名做 label 标注。不过这个过程在这里似乎都不同容易实现，且先留空。

```python
AttributeError: unexpected attribute 'border_width' to Legend, similar attributes are border_line_width, border_line_dash or label_width
```

图内叠加用 `*`，而多图堆叠也非常方便，用 `+` 即可。


```python
fig3 = (fig1.opts(height=480) + fig2).opts(toolbar=None)
fig3
```






<iframe id="fig3" frameborder=0 height="500px" width="100%" src="/files/charts/fig3.html"></iframe>



## Altair

交互式图表库，我最早用的是 [Altair](https://altair-viz.github.io/index.html)。Altair 的语法结构不算复杂，使用时也比较符合直觉， `Chart().mark_x().encode()...properties()` 一路串下去，`Chart` 吃数据，`mark_x` 吃形状，`encode` 吃配置，`properties` 做一些找补。特别有意思的是，在串联环节当中，Altair 还引入了便捷的数据变换机制，方便用户操作数据。并且文档和 API 组织的相当好，使用时没有 Plotly、Bokeh 那么松散的感觉，遇到问题时，按图索骥也比较通畅。


```python
import altair as alt

alt.__version__
```




    '5.2.0'




```python
fig4 = (
    # 吃数据
    alt.Chart(heatmap_dat.fill_null(0))
    # 画方块
    .mark_rect()
    # 微调配置
    .encode(
        # x 及其数据类型
        x="hour:O",
        # y 及其数据类型、排序方式
        y=alt.Y("month:O", sort="descending"),
        # color 填充规则
        color=alt.condition(
            "datum.n != 0", alt.Color("n:Q").scale(scheme="reds"), alt.value("white")
        ),
        # 悬停信息
        tooltip=[
            alt.Tooltip("month"),
            alt.Tooltip("hour"),
            alt.Tooltip("n:Q", title="# of videos"),
        ],
    )
    # 补充定义
    .properties(title="Heatmap", width=500, height=300)
)

fig5 = (
    alt.Chart(bump_dat)
    # 画线、标点
    .mark_line(point=True)
    .encode(
        x="year:O",
        y=alt.Y("ranking:Q", sort="descending"),
        # 填充且干预排序
        color=alt.Color(
            "cat_name",
            sort=[
                "Science & Technology",
                "People & Blogs",
                "Film & Animation",
                "Education",
                "Gaming",
                "Entertainment",
                "Howto & Style",
                "Music",
                "Travel & Events",
                "Sports",
                "News & Politics",
                "Pets & Animals",
                "Autos & Vehicles",
                "Comedy",
                "Nonprofits & Activism",
            ],
        ),
        tooltip=[
            alt.Tooltip("year:O"),
            alt.Tooltip("cat_name"),
            alt.Tooltip("ranking:Q"),
        ],
    )
    .properties(
        title="Bump Chart",
        width=500,
        height=300,
    )
)

# 超方便的多图堆叠
# (fig1 & fig2)
fig6 = (fig4 | fig5).configure_axis(grid=False).properties(title="Bye, hell subplots.")
fig6
```





<iframe id="fig6" frameborder=0 height="420px" width="100%" src="/files/charts/fig6.html"></iframe>


如上可见，图+图的堆叠及操控尤其方便，直出画面的效果简洁大方。缺点就是数据量一大（5000）就报警。

```python
MaxRowsError: The number of rows in your dataset is greater than the maximum allowed (5000).

Try enabling the VegaFusion data transformer which raises this limit by pre-evaluating data
transformations in Python.
    >> import altair as alt
    >> alt.data_transformers.enable("vegafusion")

Or, see https://altair-viz.github.io/user_guide/large_datasets.html for additional information
on how to plot large datasets.
```

## Plotly

Plotly 为满足「灵活自定义」以及「开箱即用」的两种需求，提供了两种不同的途径。一种底层 API，强调精确控制，称作 graphic_objects；一种上层 API，强调简单易用，称作 express。上一篇文章的全部图表，均出自 Plotly 框架。一般是先用 express 尝试，express 表达不出来的图表，再使用 graphic_objects 描绘。


```python
import plotly

plotly.__version__
```




    '5.18.0'




```python
import plotly.graph_objects as go
from plotly import express as px
from plotly.subplots import make_subplots
```


```python
fig7 = (
    # 方块热力图在 plotly express 中表述为 imshow
    px.imshow(
        # 为了迎合 imshow 的表达方式，我们需要先揉捏一下数据
        heatmap_dat.pivot(index="month", columns="hour", values="n")
        .to_pandas()
        .set_index("month"),
        # 数据呈现方式
        labels=dict(x="hour", y="month", color="n_videos"),
        color_continuous_scale="reds",
        # 图表配置
        template="plotly_white",
        title="Heatmap",
    ).update_layout(
        xaxis1=dict(showgrid=False),
        yaxis1=dict(showgrid=False),
    )
)

fig7
```


<iframe id="fig7" frameborder=0 height="450px" width="100%" src="/files/charts/fig7.html"></iframe>


在 Plotly 里边实现 Bump Chart 也需要我们自行设计实现方式，这时候就需要从 express 切换到 graphic_objects。这一切换过程往往会给我们造成一些困扰，主要来源于 API 设计上的不完全兼容，比如同样是绘制散点图，这两套 API 散点图方法提供的参数可能就不一致，从而造成理解偏差。


```python
def plotly_bump(df):
    fig = go.Figure()

    # Bump Chart 可以
    # 为每一个类别
    for name, data in df.group_by(["cat_name"]):
        name = name[0]
        trace = go.Scatter(
            x=data["year"],
            y=data["ranking"],
            mode="lines+markers",
            name=name,
            line=dict(width=2),
            marker=dict(size=10),
            text=name,
            hoverinfo="text",
            legendrank=data["ranking"][-1],
        )
        fig.add_trace(trace)

    fig.update_layout(
        title="Bump Chart",
        xaxis=dict(title="Year"),
        yaxis=dict(title="Rank"),
        legend=dict(title="Category"),
        yaxis_autorange="reversed",
        template="plotly_white",
        height=500,
        width=800,
        xaxis1=dict(showgrid=False),
        yaxis1=dict(showgrid=False),
    )

    return fig


fig8 = plotly_bump(bump_dat)
fig8
```


<iframe id="fig8" frameborder=0 height="500px" width="100%" src="/files/charts/fig8.html"></iframe>


可见，Plotly 设计的 figure、trace、layout, 及其各项属性都是自成一派的，它有自己鲜明的特色，默认的交互效果也非常好，只不过 ggplot2 这一脉的用户，需要一个习惯的过程。快速分析时我很喜欢用 Plotly 做可视化，但是遇到自定义场景时又很头痛，往往需要耗费很多时间去搜索、查阅。


```python
def plotly_subplots():
    fig = make_subplots(rows=1, cols=2, subplot_titles=["Heatmap", "Bump Chart"])

    # Heatmap
    heatmap_trace = go.Heatmap(
        x=heatmap_dat["hour"],
        y=heatmap_dat["month"],
        z=heatmap_dat["n"],
        hoverongaps=False,
        opacity=0.7,
        colorscale="reds",
        colorbar=dict(x=0.45, y=0.5, len=1.1),
    )
    fig.add_trace(
        heatmap_trace,
        row=1,
        col=1,
    )

    # Bump Chart
    for name, data in bump_dat.group_by(["cat_name"]):
        name = name[0]
        trace = go.Scatter(
            x=data["year"],
            y=data["ranking"],
            mode="lines+markers",
            name=name,
            line=dict(width=2),
            marker=dict(size=10),
            text=name,
            hoverinfo="text",
            legendrank=data["ranking"][-1],
            legendgroup="2",
        )
        fig.add_trace(trace, row=1, col=2)

    # Update layout
    fig.update_layout(
        title_text="Heatmap and Bump Chart Side by Side",
        template="plotly_white",
        xaxis1=dict(showgrid=False),
        yaxis1=dict(showgrid=False),
        yaxis2_autorange="reversed",
        height=400,
        width=1300,
    )

    return fig


fig9 = plotly_subplots()
fig9
```


<iframe id="fig9" frameborder=0 height="500px" width="100%" src="/files/charts/fig9.html"></iframe>


花了不少时间让上面的两幅图表排在一起，结论暂时只有一个：不要折腾 Plotly 的 subplots，会变得不幸。

## Bokeh


```python
import bokeh

bokeh.__version__
```




    '3.3.3'



不像绝大多数的图表库，Bokeh 已经不再内置模板 API，即便最常见的散点图、折线图，使用 Bokeh 来实现，看起来也没有那么直观，给人一种从底层一砖一瓦开始的基建感。



```python
from bokeh.layouts import row
from bokeh.models import ColumnDataSource, Legend, LegendItem
from bokeh.plotting import figure, show, save
from bokeh.transform import factor_cmap, linear_cmap
from bokeh.io import output_notebook

output_notebook()
```


<style>
    .bk-notebook-logo {
        display: inline-block;
        width: 20px;
        height: 20px;
        background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABQAAAAUCAYAAACNiR0NAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAABx0RVh0U29mdHdhcmUAQWRvYmUgRmlyZXdvcmtzIENTNui8sowAAAOkSURBVDiNjZRtaJVlGMd/1/08zzln5zjP1LWcU9N0NkN8m2CYjpgQYQXqSs0I84OLIC0hkEKoPtiH3gmKoiJDU7QpLgoLjLIQCpEsNJ1vqUOdO7ppbuec5+V+rj4ctwzd8IIbbi6u+8f1539dt3A78eXC7QizUF7gyV1fD1Yqg4JWz84yffhm0qkFqBogB9rM8tZdtwVsPUhWhGcFJngGeWrPzHm5oaMmkfEg1usvLFyc8jLRqDOMru7AyC8saQr7GG7f5fvDeH7Ej8CM66nIF+8yngt6HWaKh7k49Soy9nXurCi1o3qUbS3zWfrYeQDTB/Qj6kX6Ybhw4B+bOYoLKCC9H3Nu/leUTZ1JdRWkkn2ldcCamzrcf47KKXdAJllSlxAOkRgyHsGC/zRday5Qld9DyoM4/q/rUoy/CXh3jzOu3bHUVZeU+DEn8FInkPBFlu3+nW3Nw0mk6vCDiWg8CeJaxEwuHS3+z5RgY+YBR6V1Z1nxSOfoaPa4LASWxxdNp+VWTk7+4vzaou8v8PN+xo+KY2xsw6une2frhw05CTYOmQvsEhjhWjn0bmXPjpE1+kplmmkP3suftwTubK9Vq22qKmrBhpY4jvd5afdRA3wGjFAgcnTK2s4hY0/GPNIb0nErGMCRxWOOX64Z8RAC4oCXdklmEvcL8o0BfkNK4lUg9HTl+oPlQxdNo3Mg4Nv175e/1LDGzZen30MEjRUtmXSfiTVu1kK8W4txyV6BMKlbgk3lMwYCiusNy9fVfvvwMxv8Ynl6vxoByANLTWplvuj/nF9m2+PDtt1eiHPBr1oIfhCChQMBw6Aw0UulqTKZdfVvfG7VcfIqLG9bcldL/+pdWTLxLUy8Qq38heUIjh4XlzZxzQm19lLFlr8vdQ97rjZVOLf8nclzckbcD4wxXMidpX30sFd37Fv/GtwwhzhxGVAprjbg0gCAEeIgwCZyTV2Z1REEW8O4py0wsjeloKoMr6iCY6dP92H6Vw/oTyICIthibxjm/DfN9lVz8IqtqKYLUXfoKVMVQVVJOElGjrnnUt9T9wbgp8AyYKaGlqingHZU/uG2NTZSVqwHQTWkx9hxjkpWDaCg6Ckj5qebgBVbT3V3NNXMSiWSDdGV3hrtzla7J+duwPOToIg42ChPQOQjspnSlp1V+Gjdged7+8UN5CRAV7a5EdFNwCjEaBR27b3W890TE7g24NAP/mMDXRWrGoFPQI9ls/MWO2dWFAar/xcOIImbbpA3zgAAAABJRU5ErkJggg==);
    }
</style>
<div>
    <a href="https://bokeh.org" target="_blank" class="bk-notebook-logo"></a>
    <span style="font-size:.8em;font-family:mono">Loading BokehJS ...</span>
</div>






```python
# 需要 Pandas DataFrame
fig10_dat = heatmap_dat.to_pandas()

# 控制热力图细节
fig10 = figure(
    title="Heatmap",
    # 小时
    x_range=[str(x) for x in range(0, 24)],
    # 月份，多一份留白，让每个小格子等宽等长
    y_range=[str(x) for x in range(1, 14)],
    x_axis_location="above",
    tools="",
    toolbar_location=None,
    tooltips=[("hour", "@hour"), ("month", "@month"), ("n", "@n")],
    width=600,
    height=300,
)

# 去掉干扰
fig10.grid.grid_line_color = None
fig10.axis.axis_line_color = None
fig10.axis.major_tick_line_color = None
fig10.outline_line_color = None

# 映射数据
fig10.rect(
    x="hour",
    y="month",
    width=1,
    height=1,
    source=fig10_dat,
    fill_color=linear_cmap(
        "n", "Reds256", high=fig10_dat.n.min(), low=fig10_dat.n.max(), nan_color="white"
    ),
    line_color=None,
)

# 展示图表
show(fig10)
```



<iframe id="fig10" frameborder=0 height="310px" width="100%" src="/files/charts/fig10.html"></iframe>






```python
# 初始化图表配置
fig11 = figure(
    title="Slope Graph",
    x_range=(2015.9, 2023.1),
    y_range=(17.5, 0.5),
    x_axis_location="above",
    tools="",
    toolbar_location=None,
    width=600,
    height=300,
    tooltips=[("category", "@cat_name"), ("ranking", "@ranking")],
)

# 预先设定 17 个分类的颜色映射关系
cmap6 = factor_cmap(
    "cat_name",
    "Category20_17",
    sorted(bump_dat["cat_name"].unique()),
)

# 预先设定好图例排序
fig11_legend_indexes = [
    "Science & Technology",
    "People & Blogs",
    "Film & Animation",
    "Education",
    "Gaming",
    "Entertainment",
    "Howto & Style",
    "Music",
    "Travel & Events",
    "Sports",
    "News & Politics",
    "Pets & Animals",
    "Autos & Vehicles",
    "Comedy",
    "Nonprofits & Activism",
    "Movies",
    "Trailers",
]

fig11_legend_items = []

# 分别绘制折线图和散点图，并把图例信息传出来
for name, data in bump_dat.group_by(["cat_name"]):
    src = data.to_pandas()
    name = name[0]
    line = fig11.line(
        x="year",
        y="ranking",
        source=src,
        line_width=2,
        line_color=cmap6.transform.palette[cmap6.transform.factors.index(name)],
    )
    scatter = fig11.scatter(
        x="year",
        y="ranking",
        source=src,
        size=8,
        color=cmap6.transform.palette[cmap6.transform.factors.index(name)],
    )

    fig11_legend_items.append(LegendItem(label=name, renderers=[line, scatter]))

# 排除干扰
fig11.grid.grid_line_color = None
fig11.axis.axis_line_color = None
fig11.axis.major_tick_line_color = None
fig11.outline_line_color = None
fig11.xaxis.minor_tick_out = 0
fig11.xaxis.major_tick_in = 0
fig11.yaxis.minor_tick_out = 0
fig11.yaxis.major_tick_in = 0

# 微调图例
fig11_legend_items_sorted = sorted(
    fig11_legend_items, key=lambda x: fig11_legend_indexes.index(x.label.value)
)
fig11.add_layout(Legend(items=fig11_legend_items_sorted, click_policy="mute"), "right")

# 排除干扰
fig11.legend.label_text_font_size = "6pt"
fig11.legend.glyph_height = 1
fig11.legend.spacing = 5
fig11.legend.label_height = 5
fig11.legend.location = (0, 0)
fig11.legend.background_fill_alpha = 0.6
fig11.legend.border_line_alpha = 0

# 展示图表
show(fig11)
```



<iframe id="fig11" frameborder=0 height="320px" width="100%" src="/files/charts/fig11.html"></iframe>



至于多图堆叠，bokeh 提供了清晰的[方案](https://docs.bokeh.org/en/latest/docs/user_guide/basic/layouts.html)，比如我们这里的一行两列，直接调用 row 方法即可。


```python
fig12 = row(fig10, fig11)
show(fig12)
```


<iframe id="fig12" frameborder=0 height="320px" width="100%" src="/files/charts/fig12.html"></iframe>



## 小结

今年应该会深入使用 Polars 及其背后的 hvPlot，以及 hvPlot 背后的 [HoloViz](https://holoviz.org/)、bokeh 生态，继续关注 Altair。另外，将尝试减少 Plotly 的使用，好用是好用，微调也确实痛苦。
