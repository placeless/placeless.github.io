---
layout: post
title:  "Matplotlib.basemap 地理数据可视化"
date:   2017-09-11 05:20 +0800
---

想玩一下地理数据的可视化。

工具：

- [Python 3](https://www.python.org/download/releases/3.0/)
  - [Jupyter Notebook](http://jupyter.org)
  - [Pandas](http://pandas.pydata.org)
  - [Matplotlib](https://matplotlib.org/index.html)
  - [Basemap](https://github.com/matplotlib/basemap)
  - [pyshp](https://pypi.python.org/pypi/pyshp)
- [SQLite3](https://www.sqlite.org)
- [GEOS](https://trac.osgeo.org/geos)

数据：

- [Open Flights](https://openflights.org/data.html)
- [ SQLite 形式的 Open Flights](https://www.dropbox.com/s/a2wax843eniq12g/flights.db?dl=0)
- [省市区县 Shapefiles](http://www.gadm.org)

Open Flights 官网提供的都是独立文本，形如 airports.dat, airlines.dat, routes.dat，好在 [Data Quest](https://www.dataquest.io/blog/python-pandas-databases/) 提供了一个打包的 .db 文件，我选择直接使用这一数据源。

开始：sqlite3 驱动数据库，pandas 查询数据表，numpy 总有用。

```python
import sqlite3
import pandas as pd
import numpy as np
```

```python
from mpl_toolkits.basemap import Basemap
import matplotlib.pyplot as plt
```

需要注意的是，Basemap 不比 sqlite、pandas 等，并非开箱即用，需要一点变通办法：

- Basemap 需自行安装
- PyPI 内的 basemap 版本较老，可去 [Basemap Github Repo](https://github.com/matplotlib/basemap) 安装新版
- Basemap 依赖 GEOS，虽然源码附带了 geos-3.3.3，但需手工编译
- 我选择切到 Homebrew，安装 geos-3.6.2
- 之后返回仓库，再据章安装 Basemap

配置 Jupyter Notebook 页内插图，并适配 Retina Display。

```python
%matplotlib inline
%config InlineBackend.figure_format = 'retina'
```

之前已经通过其他工具，简单了解了 flights.db 的表结构情况。这个练习，暂时只是想了解一下中国到日本的航线概况。

![about flights.db](/files/2017/09/11/about_flights.png)

一切就绪之后，连接数据库，接入两国机场数据以及航线数据。Pandas 提供的 `pd.read_sql_query` 方法省去了不少中间环节，往里面直接写 SQL 语句就行了，Cool 😎。

```python
db = sqlite3.connect('../py/data/flights.db')

airports = pd.read_sql_query(
    """
    SELECT DISTINCT cast(latitude AS float) AS lat,
       cast(longitude AS float) lon
    FROM airports
    WHERE country = 'China';
    """,
    db
)

routes = pd.read_sql_query(
    """
    SELECT DISTINCT cast(sa.latitude AS float) AS src_lat,
       cast(sa.longitude AS float) AS src_lon,
       cast(da.latitude AS float) AS dest_lat,
       cast(da.longitude AS float) AS dest_lon,
       sa.city as src_city,
       da.city as dest_city
    FROM routes
    INNER JOIN airports sa ON sa.id = routes.source_id
    AND sa.country = 'China'
    INNER JOIN airports da ON da.id = routes.dest_id
    AND da.country = 'Japan';
    """,
    db
)
```

读取了两笔数据：

1. 中国境内的所有机场经纬度
2. 涉及中日航线的两国机场经纬度

```python
airports.head()
```

|      |    lat    |    lon     |
| :--: | :-------: | :--------: |
|  0   | 40.080111 | 116.584556 |
|  1   | 32.800428 | 102.534785 |
|  2   | 49.204997 | 119.825000 |
|  3   | 39.124353 | 117.346183 |
|  4   | 37.746897 | 112.628428 |

```python
routes.head()
```

|      |  src_lat  |  src_lon   | dest_lat  |  dest_lon  | src_city | dest_city |
| :--: | :-------: | :--------: | :-------: | :--------: | :------: | :-------: |
|  0   | 40.080111 | 116.584556 | 35.764722 | 140.386389 | Beijing  |   Tokyo   |
|  1   | 40.080111 | 116.584556 | 42.775200 | 141.692283 | Beijing  |  Sapporo  |
|  2   | 40.080111 | 116.584556 | 35.552258 | 139.779694 | Beijing  |   Tokyo   |
|  3   | 40.080111 | 116.584556 | 26.195814 | 127.645869 | Beijing  |  Okinawa  |
|  4   | 40.080111 | 116.584556 | 34.858414 | 136.805408 | Beijing  |  Nagoya   |

直接画个地图试试看先，先做一个 20x20 inches 的画框打底。

然后进入 Basemap 实例。目测三大块：

- Projection
- 地图呈现区域
- 细节配置

第一条决定地图的呈现形式，中间决定呈现哪一块区域，后面的配置负责地图细节。区域部分会受到所选 Projection 的影响。

先绘上国界、海面，有个想象的基础。

```python
fig, ax = plt.subplots(figsize=(20, 20))

map = Basemap(
    projection = 'merc',
    llcrnrlat=16,
    llcrnrlon=100,
    urcrnrlat=50,
    urcrnrlon=150,
    resolution='l'
)

map.drawcountries(color='#cccccc')
map.drawlsmask(ocean_color='b', alpha=0.05)
```

![png](/files/2017/09/11/output_14_1.png)



## Map Projection

由于不可能把地表完美反映到二维纸面上，所有的地图都是各种各样扭曲的产物，把扭曲的「球面经纬坐标」抹平到「平面坐标系」的过程，称为 Projection，即下图第三至第四个步骤的转换。Basemap 提供了 [25](https://matplotlib.org/basemap/api/basemap_api.html#mpl_toolkits.basemap.Basemap) 种不同的 Projections 的实现，一时不知从何挑起，但这却是最有趣的部分。

![China and j](/files/2017/09/11/projection.png)

一张地图、一种 Projection，无论如何撕扯、扭曲，它总是为了满足人们对以下地理属性中的某一条或多条的准确衡量：

- Area 面积
- Shape 形状
- Direction 方向
- Bearing 方位（注意区别 Direction）
- Distance 距离
- Scale 缩放比例

比如我们最常见的地图，所使用的通常是 [Mercator Projection](https://en.wikipedia.org/wiki/Mercator_projection)，它是 1569 年的一个叫做 [Gerardus Mercator](https://en.wikipedia.org/wiki/Gerardus_Mercator) 的地图绘制员设计的。从 Developable Surfaces 分类讲，它是一个 Cylindrical Map Projection；从 Preserved Property 分类讲，它是一个 [Conformal (or orthomorphic) Projection](https://en.wikipedia.org/wiki/Map_projection#Conformal)。初衷是为了海上导航，所以方位方向是最重要的，方向对了，才到得了目的地。相比之下，大陆版块、国家形状、面积、距离等因素就没那么紧要了。

海上定位，方向、纬度、经度。方向看指南针，维度看星星看太阳，[经度靠中奖](http://m.sohu.com/n/409763090/)😭。

Mercator 地图的优势，就是随便在地图上的两点间画一条直线，它就是一条 [Rhumb Line](https://en.wikipedia.org/wiki/Rhumb_line)。它虽不是地表两点间最短路线（[Great Circle](https://en.wikipedia.org/wiki/Great_circle)），但与经线的角度固定，船不用一直变向，虽然有点绕远，可导航省事。而如果走 Great Circle，距离虽短了，可船就要时刻不停的变向，风险和操作难度都大增，而且当时[经度之战](http://m.sohu.com/n/409763090/)刚刚开始，钟表法、月距法都还在摸索之中。

现在定位导航不靠地图了，很多人都开始反对 Mercator 地图，因为它扭曲实在太严重了，除了赤道附近，越往两极越膨胀，观感上，格陵兰岛的面积都赶上大非洲了。

![the true size of Africa](http://kai.sub.blue/images/true_size_of_africa_v3-da3dd38e.jpg)
<figcaption>The true size of Africa</figcaption>

这里有一个网站 [The True Size Of](https://thetruesize.com/)，可以生动的体验到 Mercator 地图的面积扭曲，可以试试把中国挪到俄罗斯的纬度，把格陵兰岛拉到非洲大陆上来做对比。

### Projection 的选择

没有唯一正确答案。断断续续看了好几天的地理测绘内容，非常烧脑。简言之，既然扭曲不可避免，那么根据自己对形状、面积、距离、方位等方面的需求，像制图先驱们一样，选择保留的最准确的一个即可。此外，很多 Projection 是面向 World Map 的，如果我们的需要是区域性的，则可以先排除这一类 Projection。

另，这里还有一个网站，[Map Projection Transitions](https://www.jasondavies.com/maps/transition/)，可以直观体验到二维平面上经纬系统在各种 Projection 里面是如何扭曲和移动的。

另，Matplotlib.Basemap 文档里，针对支持的每个 Projection，也都有[样例](http://matplotlib.org/basemap/users/mapsetup.html)。

另，这里有一个 [Cheatsheet](http://www.radicalcartography.net/index.html?projectionref) 可当作选择参考。

而我这里的目的，仅是对中日航线有个画面感，勉勉强强算一个 [Thematic Map](https://en.wikipedia.org/wiki/Thematic_map)，使用 Mercator，相信问题不大，关键配置简单，一个翻转的中文引号「」即可，也不用定义中心点，左下角纬度 `llcrnrlat`，左下角经度 `llcrnrlon`，右上角纬度 `urcrnrlat`，右上角经度 `urcrnrlon`，区域就固定了。

如需在地图中绘制更细的行政区划，需另外下载省市区县一级的 [shapefiles](https://en.wikipedia.org/wiki/Shapefile)，详见 [Global Administrative Areas](http://www.gadm.org/country) 网站。

```python
chn = map.readshapefile('../shapefile/CHN_adm_shp/CHN_adm1', 'chn', color='#555555', linewidth=0.1, ax=ax)
jpn = map.readshapefile('../shapefile/JPN_adm_shp/JPN_adm1', 'jpn', color='#555555', linewidth=0.1, ax=ax)
fig
```

![png](/files/2017/09/11/output_16_0.png)

把国内各省市机场经纬度转换为二维坐标，绘制到地图上。

```python
x, y = map(list(airports['lon']),
         list(airports['lat']))

map.scatter(
  x,
  y,
  50,
  marker='.',
  color='y',
  alpha=0.2,
  ax=ax
)

fig
```

![png](/files/2017/09/11/output_18_0.png)

绘制中日航线：以出发城市为航线分组，分别绘制 Great Circle，标以不同颜色。

```python
grouped = routes.groupby('src_city')
src_city_list = grouped.groups.keys()

colors_length = np.random.random((len(src_city_list), 3))
colors = dict([(city, color) for city, color in zip(src_city_list, colors_length)])

for key, group in grouped:
    for index, row in group.iterrows():
        map.drawgreatcircle(
            row['src_lon'],
            row['src_lat'],
            row['dest_lon'],
            row['dest_lat'],
            linewidth=1,
            c=colors[key],
            alpha=0.3,
            ax=ax
        )

fig
```

![png](/files/2017/09/11/output_20_0.png)

感觉差点啥，标记一下国内出发城市名。

```python
src_labels = routes.loc[:, ['src_lat', 'src_lon', 'src_city']]
src_labels.drop_duplicates(subset=['src_city'], keep='first', inplace=True)
src_labels.reset_index(drop=True, inplace=True)

Xs, Ys = map(list(src_labels['src_lon']), list(src_labels['src_lat']))

for i, (x, y) in enumerate(zip(Xs, Ys)):
    ax.annotate(str(src_labels['src_city'][i]), (x,y), xytext=(0, 5), textcoords='offset points', alpha=0.8, size='small') 

fig
```

![png](/files/2017/09/11/output_22_0.png)

一视同仁，再标记一下日本到达机场城市名。

```python
dest_labels = routes.loc[:, ['dest_lat', 'dest_lon', 'dest_city']]
dest_labels.drop_duplicates(subset=['dest_city'], keep='first', inplace=True)
dest_labels.reset_index(drop=True, inplace=True)

Xd, Yd = map(list(dest_labels['dest_lon']), list(dest_labels['dest_lat']))

for i, (x, y) in enumerate(zip(Xd, Yd)):
    ax.annotate(str(dest_labels['dest_city'][i]), (x,y), xytext=(5, 0), textcoords='offset points', alpha=0.8, size='x-small')

fig
```

![png](/files/2017/09/11/output_24_0.png)

图片上右键 > Open Image in New Tab 可单开大图。

暂时记录至此。