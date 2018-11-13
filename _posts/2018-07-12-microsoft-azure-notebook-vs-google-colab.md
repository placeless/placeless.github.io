---
layout: post
title:  Azure notebook vs Google colab
date:   2018-07-12 00:41 +0800
redirect_from:
  - 2018/07/12/microsoft-azure-notebook-vs-google-colab
---

![Jupyter Notebook](http://jupyter.org/assets/jupyterpreview.png)

<figcaption>图片来自于 https://jupyter.org/ </figcaption>

对于数据分析人员来说，[Jupyter Notebook](https://jupyter.org/) 也是一个「用了就再也回不去」的典型工具之一。现在很多云服务平台，比如微软、谷歌都免费提供了 Notebook 的在线环境，注册开通都极其容易，几乎没有门槛，这对于本地配置困难、以及有在线协作需求的用户来说，都是极有吸引力选项。以上两家我刚好都有使用，在此做一个简单对比。

第一条，特点差异。微软 [Azure Notebook](https://notebooks.azure.com/) 几乎就是一个原汁原味的 Jupyter Notebook；而谷歌 [Colab](https://colab.research.google.com/) 更强调研究、教育和分享协作，所以在设计上有一些变化，比如内核暂时只支持 Python2/3，界面更轻盈好看一些，快捷键也不同以往，好在可自定义。总之这一项见仁见智，无论高下。

![Azure Notebook](https://notebooks.azure.com/content/seaborn_front_laptop.png)

<figcaption>Azure Notebook，图片来自于 https://notebooks.azure.com/</figcaption>

第二条，使用通畅，Azure Notebook 胜。因为它不用翻墙就能访问，而在 google.com 子域下的谷歌 Colab 需要用户懂得轻身功夫。

第三条，文件管理，Azure Notebook 略胜。因为它操作起来，跟使用网盘差不多，调用自己上传的数据文件，跟加载本地文件一样自然；但 Colab 没法直接上传数据文件，而且调用自家 Google Drive 里面的数据，也需经过一番引用和配置，有一点复杂，有一点耗时。

<details>
<summary>展开查看 Colab 上传文件范例</summary>
<pre>
<code>
#
# Python
#
from google.colab import files

uploaded = files.upload()

for fn in uploaded.keys():
  print('User uploaded file "{name}" with length {length} bytes'.format(
      name=fn, length=len(uploaded[fn])))
      
#
# Output
#
data-MKOez.csv(text/csv) - 6039 bytes, last modified: n/a - 100% done
data-OHT21.csv(text/csv) - 19267 bytes, last modified: n/a - 100% done
data-OXy0O.csv(text/csv) - 105926 bytes, last modified: n/a - 100% done

Saving data-MKOez.csv to data-MKOez.csv
Saving data-OHT21.csv to data-OHT21.csv
Saving data-OXy0O.csv to data-OXy0O.csv
User uploaded file "data-MKOez.csv" with length 6039 bytes
User uploaded file "data-OHT21.csv" with length 19267 bytes
User uploaded file "data-OXy0O.csv" with length 105926 bytes

#
# Bash
#
$ !ls
datalab  data-MKOez.csv  data-OHT21.csv  data-OXy0O.csv
</code>
</pre>
</details>

![Google Colaboratory](https://research.google.com/colaboratory/screenshot.png)

<figcaption>👆这里有一张来自于 https://research.google.com/colaboratory/ 的图片，你看见了吗</figcaption>

第四条，硬件配置，Colab 胜。Colab 提供了双核四线程的至强 2.3GHz 处理器，12G 内存，360G 的磁盘空间，还可以配置 GPU 加速 😱；而 Azure Notebook 只有双核两线程的同级处理器，仅 4G 内存和 20G 磁盘存储。使用时，明显感觉得出来 Colab 要比 Azure Notebook 快不少。

<details>
<summary>展开配置详情</summary>
<pre>
<code>
#
# Azure Notebook
#
$ !lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                2
On-line CPU(s) list:   0,1
Thread(s) per core:    1
Core(s) per socket:    2
Socket(s):             1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 79
Model name:            Intel(R) Xeon(R) CPU E5-2673 v4 @ 2.30GHz
Stepping:              1
CPU MHz:               2294.685
BogoMIPS:              4589.37
Hypervisor vendor:     Microsoft
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              51200K
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic…


$ !free -m
              total        used        free      shared  buff/cache   available
Mem:           3917         181        3332          17         403        3440
Swap:             0           0           0

$ !df -h
Filesystem      Size  Used Avail Use% Mounted on
overlay          20G  127M   19G   1% /
tmpfs            64M     0   64M   0% /dev
tmpfs           2.0G     0  2.0G   0% /sys/fs/cgroup
shm             2.0G     0  2.0G   0% /dev/shm
tmpfs           2.0G     0  2.0G   0% /sys/firmware

#
# Google Colab
#

$ !lscpu
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              2
On-line CPU(s) list: 0,1
Thread(s) per core:  2
Core(s) per socket:  1
Socket(s):           1
NUMA node(s):        1
Vendor ID:           GenuineIntel
CPU family:          6
Model:               63
Model name:          Intel(R) Xeon(R) CPU @ 2.30GHz
Stepping:            0
CPU MHz:             2300.000
BogoMIPS:            4600.00
Hypervisor vendor:   KVM
Virtualization type: full
L1d cache:           32K
L1i cache:           32K
L2 cache:            256K
L3 cache:            46080K
NUMA node0 CPU(s):   0,1
Flags:               fpu vme de pse tsc msr pae mce cx8 apic…

$ !free -m
              total        used        free      shared  buff/cache   available
Mem:            12G        1.0G        1.4G        250M         10G         11G
Swap:            0B          0B          0B

$ !df -h
Filesystem      Size  Used Avail Use% Mounted on
overlay         359G  6.0G  335G   2% /
tmpfs           6.4G     0  6.4G   0% /dev
tmpfs           6.4G     0  6.4G   0% /sys/fs/cgroup
tmpfs           6.4G  249M  6.2G   4% /opt/bin
/dev/sda1       365G  7.7G  358G   3% /etc/hosts
shm              64M     0   64M   0% /dev/shm
tmpfs           6.4G     0  6.4G   0% /sys/firmware
</code>
</pre>
</details>

第五条，软件版本，Colab 胜。主要是 Azure Notebook 自带的 `jupyter` 以及 `notebook` 等包版本较老，目测是集成了整套 Anaconda，关键是升级不畅，在使用 Altair 作图时，我遇到了依赖问题，调试半天，加之运行反馈又慢，实在令人心焦。而 Colab 在使用使用和更新当中，基本没遇到什么大问题。

## 小结

如果是墙内的简单体验，推荐 Azure Notebook；如果是常用、追求性能，那推荐上 Google Colab，完成度、性能、易用性等方面都要强上不少。