---
layout: post
title:  "CDEC Dota1 大师赛的匹配机制问题"
date:   2017-10-22 16:57 +0800
---

看到不少人抱怨这届 CDEC 匹配机制有问题。好奇，打算从数据上验证一下。

数据爬自 VP Game 的[比赛记录](http://dota1.vpgame.com/league/season/40.html)。爬取过程特别简单，浏览器看一下 HTTP 请求，即可发现每页排名的 json 文件，这里直接使用 curl 下载，如遇 502，再运行一下失败的任务即可，没有再写爬虫：

```bash
curl 'http://dota1.vpgame.com/v1/dota1/league/match_lists/40/[1-60]-10.json' -o '#1.json'
```

下来的 60 个 json 文件，先用 [jq](https://stedolan.github.io/jq/) 读取必要字段：

```bash
jq '.body.lists[] | .id + "," + .victory + "," + .duration + "," + .players.blue[].player_id + "," + .players.red[].player_id' cdec/*.json > matches.txt
```

然后还是在终端内，通过 sublime text、cut、sort、uniq 做简要清理和统计。

最后进入到 Jupyter Notebook 通过 Python 做简要分析。

```python
import pandas as pd
import seaborn as sns

%matplotlib inline
%config InlineBackend.figure_format = 'retina'
plt.style.use('seaborn')
```

```python
df = pd.read_csv('matches1.csv', names=['match_id', 'winner', 'duration', 'blue', 'red'])
df.head()
```

|      | match_id | winner | duration | blue   | red   |
| ---- | -------- | ------ | -------- | ------ | ----- |
| 0    | 21208    | red    | 2161     | 心疼丶我   | 南昌sf丶 |
| 1    | 21208    | red    | 2161     | Mia小宝贝 | 南昌sf丶 |
| 2    | 21208    | red    | 2161     | 舔痰达人   | 南昌sf丶 |
| 3    | 21208    | red    | 2161     | 科比x丶   | 南昌sf丶 |
| 4    | 21208    | red    | 2161     | C轩少    | 南昌sf丶 |

从每局游戏时长来看，并无明显异常值。

```python
sns.violinplot(df['duration'])
```

![duration distribution](/files/2017/10/22/duration.png)

但接下来看两方的胜负关系，问题就明显了：天灾方的胜率是近卫方的 2.5 倍。

```python
w_red = df[df['winner'] == 'red']['match_id'].nunique() # red -> 天灾
w_blue = df[df['winner'] == 'blue']['match_id'].nunique() # blue -> 近卫
print('总局数：{}'.format(w_red + w_blue))
print('天灾胜：{}'.format(w_red))
print('近卫胜：{}'.format(w_blue))
print('天灾/近卫：{:.1f}'.format(w_red/w_blue))

# 总局数：596
# 天灾胜：428
# 近卫胜：168
# 天灾/近卫：2.5
```

就 Dota 地图而言，发展至今，天灾无论如何不可能有如此大的出生优势。

目前排在第一位的，幸运的子阳，**一次近卫也没去过😯**，同样的情况还发生在排在第一页的 Zai、唯爱雨灬 等人。

```python
len(df[df['blue'] == '壹贰零叁'])

# 0
```

同样第一页的前 10 名，去过近卫的，次数也很少：

```python
df[df['red'] == '来打死爹|r'].groupby('winner')['match_id'].nunique() # 5
df[df['red'] == '皮孩123'].groupby('winner')['match_id'].nunique() # 5
```

最纠结的要数「大熊123」了，将近一半的比赛场次凑巧在近卫，居然还有惊人的 46% 的胜率：

```python
df[df['red'] == 'Sweety丶Hy大熊'].groupby('winner')['match_id'].nunique()

# winner
# blue     4
# red     12
# 天灾胜率 75%

df[df['blue'] == 'Sweety丶Hy大熊'].groupby('winner')['match_id'].nunique()

# winner
# blue    6
# red     7
# 近卫胜率 46%
```

## 总结

目前来看，问题应该是确定有的：机制把厉害的人一直排到一起，并放在了天灾方。

还没有看是否哪些人一直被排一起。