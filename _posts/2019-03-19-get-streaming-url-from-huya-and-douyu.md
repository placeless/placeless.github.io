---
layout: post
title:  "获取虎牙斗鱼的直播源地址"
date:   2019-03-19 22:37 +0800
---

常看 Dota2 比赛，奈何网页版面，花花绿绿，欣赏不来，更加不需要弹幕，所以尝试提取了一下虎牙、斗鱼两地的直播源地址，通过 IINA 来播放。下文提取的都是最高清晰度。

## 虎牙

做成了 Bookmarklet 小脚本，方便在书签/收藏夹内直接使用，macOS Safari、Firefox 测试无问题。新建一个书签，地址字段填入下面的代码即可。

```
javascript: (function() {
  function getLiveUrl(cfg) {
    let liveInfo = cfg.stream.data[0].gameStreamInfoList;
    let urls = ["请选择一个地址，复制粘贴至 IINA 等播放器："];
    for (let item of liveInfo) {
      let liveUrl = `${item.sHlsUrl}/${item.sStreamName}.m3u8`;
      urls.push(liveUrl);
    }
    alert(urls.join('\n\n\n'));
  }
  getLiveUrl(hyPlayerConfig);
})()
```

进入直播间网页之后，点击 Bookmarklet，网页即会弹出如下浮层，复制任意一个地址，填到 IINA 等播放器的 Open URL 窗口内即可。

![huya](/files/2019/huya.png)

Alfred 用户也可以做成 Workflow 来实现自动化，比如，建立一个 Hotkey Trigger，Argument 选择 Selection in macOS，然后指向一个 Run Script：`open "iina://weblink?url=$1"`，如此，我们就实现了如下效果：选中浮层上的一个地址，按下所设置的快捷键，IINA 将会自动播放所选地址，免去了复制粘贴的麻烦。

## 斗鱼

暂时手动操作，步骤如下图：

1. 进入直播间网页，打开浏览器开发工具
2. 选择一个清晰度进行播放
3. Network 标签 XHR 项目下搜索房间号，找到对应的 json 请求
4. 把 `rtmp_url` 和 `rtmp_live` 串起来丢到 IINA 即可

![douyu](/files/2019/douyu.png)

`rtmp_live` 当中的 `_1024p` 即表示最高清晰度。