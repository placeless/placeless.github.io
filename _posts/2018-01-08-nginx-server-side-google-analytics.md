---
layout: post
title:  从 Ngnix 服务器端发送 GA Hits
date:   2018-01-08 19:40 +0800
---

为了更可靠的数据采集，之前已经尝试过两种非标准的 GA Hits 发送方式：

- [借助 Measurement Protocol 防 GA 拦截](/blog/across-the-firewall-with-measurement-protocol.html#article)
- [GA SDK 的本地部署](/blog/deploy-google-analytics-sdk-on-local-server.html#article)


今天在收藏夹翻到一篇《[Ng­inx 内配置 Google An­a­lyt­ics 指南](https://darknode.in/network/nginx-google-analytics/)》，于是搭了一个虚拟机，也尝试了一下。试验环境简介及搭建：

- Host: macOS High Sierra 10.13.2
- Guest: Virtualbox 5.2.4 + nginx/1.12.1 (Ubuntu 17.10 server)
- [Install Nginx on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04)


Ubuntu Server 安装时如勾选了 LAMP，启动 Nginx 注意先关闭 Apache 避免端口冲突 `systemctl stop apache2.service`。此外，如果打算完全从 Mac 终端 SSH 去控制虚拟机，并访问测试服务器网页，需要配置端口转发 `Network - NAT - Port Forwarding`。举例如下：

| Name  | Protocol | Host IP  | Host Port | Guest IP  | Guest Port |
| :---: | :------: | :------: | :-------: | :-------: | :--------: |
|  SSH  |   TCP    | 10.0.0.2 |   2222    | 10.0.2.15 |     22     |
| Nginx |   TCP    | 10.0.0.2 |   4001    | 10.0.2.15 |     80     |

具体的实现，则全如《[Ng­inx 内配置 Google An­a­lyt­ics 指南](https://darknode.in/network/nginx-google-analytics/)》所言，如果是默认配置下测试，需照章修改的文件是 `sudo vim /etc/nginx/sites-enabled/default`。**需特别注意的是**：`@tracker` 位置需增加 `resolver`，不然无法解析远程地址；其次注意 `userid_domain`，它是 Cookie ID，跟域相关，测试环境写 IP 也可以，乱填会导致每打开一个页面统计一个 `new user`。然后打开 GA 实时报告，看 Nginx 服务器端发起的页面访问 Hits 是否被 GA 正常接收即可。

测试过程中可能还会遇到其他意外，注意多看 `tail /var/log/nginx/error.log` 等日志。

```
http {
  map $http_accept_language $lang {~^([a-zA-Z-]*) $1;
  }

  server {
    userid         on;
    userid_name    uid;
    userid_domain  example.com;
    userid_path    /;
    userid_expires 365d;
    userid_p3p     'policyref="/w3c/p3p.xml", CP="CUR ADM OUR NOR STA NID"';

    location @tracker {
      resolver 8.8.8.8;
      internal;
      proxy_method POST;
      proxy_pass https://www.google-analytics.com/collect?v=1&tid=UA-XXXXXXXX-Y&$uid_set$uid_got&t=pageview&dh=$host&dp=$uri&uip=$remote_addr&dr=$http_referer&ul=$lang&z=$msec;
      proxy_set_header User-Agent $http_user_agent;
      proxy_pass_request_headers off;
      proxy_pass_request_body off;
    }

    location / {
      try_files $uri $uri/ =404;
      post_action @tracker;
    }
  }
}
```

另有一篇延伸阅读：[How To Target Your Users with Nginx Analytics and A/B Testing](https://www.digitalocean.com/community/tutorials/how-to-target-your-users-with-nginx-analytics-and-a-b-testing)

