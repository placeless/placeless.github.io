---
layout: post
title:  "命令行与压缩文件们"
date:   2020-06-19 02:33 +0800
---

## 压缩 

别人不定期会同步很多 CSV 给我，每个超过 1GB，用不了多久，磁盘见满，批量压缩就很必要：

```
for f in *.csv; do 7z a -tgzip "$f.zip" "$f"; done
```

7z 的命令行形式，是我在 macOS 上偏好的跟压缩文件打交道的方式。偶尔遇到意外情况，比如解压报错，或是网上下载的字幕 rar，这时候会选择 unar。这两个工具都可以通过 Homebrew 获得。

上面 `-t` 参数之后，跟的是具体的压缩方式，默认是 7z，其他还有 zip、gzip、bzip2 以及 tar 等。如果没有 `-t` 参数，7z 也会根据输入的压缩文件后缀来给定一个压缩方式。

## 预览

但压缩始终是个复杂话题，由于疏忽或是命名不规范，即便都是 .zip 后缀的文件，可能内含并不一致，处理起来，也要见机行事。

想要知道一个压缩文件的信息，则可以通过 zipinfo 或者 file 命令来获得：

```
$ file *.zip
a.zip: gzip compressed data, was "affin...
b.zip: Zip archive data, at least v?[0x314] to extract
c.zip: 7-zip archive data, version 0.4
```

```
$ zipinfo b.zip
Archive:  b.zip
Zip file size: 1659 bytes, number of entries: 1
-rw-r--r--  6.3 unx     9898 bx defN 20-Apr-23 00:09 affinity_categories.csv
1 file, 9898 bytes uncompressed, 1479 bytes compressed:  85.1%
```

上面的 a.zip，b.zip，c.zip，都是同一个文件的不同压缩方式，如果要预览压缩文件里边的内容，需要我们根据压缩方式，区别对待：

对于使用 gzip 压缩的 a.zip 文件，macOS 上可使用 gunzip 或 gzcat（非zcat）再搭配 head 或者 less 命令来预览内容：

```
gzcat a.zip | head -5
# or 
gunzip -c a.zip | less
```

而对于使用 Zip 压缩的 b.zip 文件，需使用 unzip：

```
unzip -c b.zip | less
```

对于 7z，预览要更麻烦一点，可安装 [7zcat](https://kaos.sh/7zcat/SOURCES/7zcat) 来实现：

```
7zcat c.zip | less
```

## 文件列表

如果压缩包内有多个文件，可以先通过 `unzip -l x.zip`  或者 `tar -tvf x.zip` 参数来了解文件名情况，前者仅支持 Zip 压缩，后者支持 Zip 和 7z，但二者都不支持 gzip。但如果安装了 7z（brew install p7zip），我们可以通过 7z 来获得以上全部 3 种压缩包内的文件列表。

## 搜索

常用的 grep 命令也同样可以用于压缩文件的搜索，zgrep 面向 gzip，zipgrep 面向 Zip，7z 则暂时还没学到应对的方法。

```
zgrep Love a.gz | head -3
zipgrep Love b.zip | less
```

更复杂的一些的数据处理工具，比如 Pandas，可以使用 read_csv 等方法直接读取压缩数据，指定 `compression=` 参数即可，不过 7z 需要其他库的支持。

## 小结

压缩时指定更清楚的后缀，后续处理会更方便一些，gzip 用 .gz，Zip 用 .zip，7-zip 用 .7z；总的来说，好像 gzip/Zip 更容易处理一点。
