---
layout: post
title:  "IDFA 新变化备忘"
date:   2016-08-03 22:05 +0800
---

<div id="forfun">
    <div class="label" id="header">
        <div id="label-nav" class="blue"><b style="font-size:20px;">&lt;</b> Privacy</div>
        <div id="label-ad">Advertising</div>
        <!--<span id="label-ad">Advertising</span>-->
    </div>
    <div class="label" id="limit">
        <div id="label-title">Limit Ad Tracking</div>
        <div id="toggles">
            <input type="checkbox" name="checkbox1" id="checkbox1" class="ios-toggle" checked/>
            <label for="checkbox1" class="checkbox-label" data-off="隐私保护已关闭" data-on="隐私保护已打开"></label>
    
        </div>
    </div>
    <div class="label" id="reset">
        <div id="label-reset" class="blue">Reset Advertising Identifier...</div>
        <p class="blue" id="label-about"><a href="https://support.apple.com/en-us/HT205223">About Advertising & Privacy...</a></p>
    </div>

</div>

<style>

    .checkbox-label,*:before,*:after {
        box-sizing:border-box;
        margin:0;
        padding:0;
        /*transition*/
        -webkit-transition:.25s ease-in-out;
           -moz-transition:.25s ease-in-out;
             -o-transition:.25s ease-in-out;
                transition:.25s ease-in-out;
        outline:none;
        font-family:'Lantinghei SC','Helvetica Neue',helvetica,arial,verdana,sans-serif;
        line-height: 1em;
        letter-spacing: 0;
    }
     
    #toggles{
        float: right;
        width:40px;
        margin:8px 10px;
        text-align:center;
    }
    .ios-toggle,.ios-toggle:active{
        position:absolute;
        top:-5000px;
        height:0;
        width:0;
        opacity:0;
        border:none;
        outline:none;
    }
    
    .checkbox-label{
        display:block;
        position:relative;
        padding:10px;
        font-size:12px;
        width:100%;
        height:24px;
        /*border-radius*/
        -webkit-border-radius:18px;
           -moz-border-radius:18px;
                border-radius:18px;
        background:#f8f8f8;
        cursor:pointer;
    }
    .checkbox-label:before{
        content:'';
        display:block;
        position:absolute;
        z-index:1;
        text-indent:40px;
        height:24px;
        width:24px;
        /*border-radius*/
        -webkit-border-radius:100%;
           -moz-border-radius:100%;
                border-radius:100%;
        top:0px;
        left:0px;
        right:auto;
        text-align: right;
        background:white;
        /*box-shadow*/
        -webkit-box-shadow:0 3px 3px rgba(0,0,0,.2),0 0 0 2px #dddddd;
           -moz-box-shadow:0 3px 3px rgba(0,0,0,.2),0 0 0 2px #dddddd;
                box-shadow:0 3px 3px rgba(0,0,0,.2),0 0 0 2px #dddddd;
    }
    .checkbox-label:after{
        content:attr(data-off);
        display:block;
        position:absolute;
        z-index:0;
        top:0;
        left:-300px;
        padding:6px 10px;
        height:100%;
        width:300px;
        text-align:right;
        color:#bfbfbf;
        white-space:nowrap;
    }
    .ios-toggle:checked + .checkbox-label{
        /*box-shadow*/
        -webkit-box-shadow:inset 0 0 0 20px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
           -moz-box-shadow:inset 0 0 0 20px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
                box-shadow:inset 0 0 0 20px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
    }
    .ios-toggle:checked + .checkbox-label:before{
        left:calc(100% - 24px);
        /*box-shadow*/
        -webkit-box-shadow:0 0 0 2px transparent,0 3px 3px rgba(0,0,0,.3);
           -moz-box-shadow:0 0 0 2px transparent,0 3px 3px rgba(0,0,0,.3);
                box-shadow:0 0 0 2px transparent,0 3px 3px rgba(0,0,0,.3);
    }
    .ios-toggle:checked + .checkbox-label:after{
        content:attr(data-on);
        left:-104px;
        text-align:right;
        position:absolute;
        width:104px;
    }
    /* GREEN CHECKBOX */
    
    #checkbox1 + .checkbox-label{
    /*box-shadow*/
    -webkit-box-shadow:inset 0 0 0 0px rgba(68,219,94,1),0 0 0 2px #dddddd;
       -moz-box-shadow:inset 0 0 0 0px rgba(68,219,94,1),0 0 0 2px #dddddd;
            box-shadow:inset 0 0 0 0px rgba(68,219,94,1),0 0 0 2px #dddddd;
    }
    #checkbox1:checked + .checkbox-label{
        /*box-shadow*/
        -webkit-box-shadow:inset 0 0 0 18px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
           -moz-box-shadow:inset 0 0 0 18px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
                box-shadow:inset 0 0 0 18px rgba(68,219,94,1),0 0 0 2px rgba(68,219,94,1);
    }
    #checkbox1:checked + .checkbox-label:after{
        color:rgba(68,219,94,1);
    }
    
    #forfun {
        background: #efeff4;
        height: 300px;
        width: 320px;
        margin: 15px auto;
        border: 1px solid #c3c2c7;
        padding: 0;
    }
    .label {
        background: #fff;
        height: 40px;
        margin: 30px 0;
        border-top: 1px solid #c3c2c7;
        border-bottom: 1px solid #c3c2c7;
    }
    #label-title {
        float: left;
    }
    
    #label-ad,
    #label-title,
    #label-nav,
    #label-reset,
    #label-about {
        padding: 0 10px;
        margin-top: 12px;
        font-size: 14px;
        font-family:'Helvetica Neue',helvetica,arial,verdana,sans-serif;
        line-height: 1em;
        letter-spacing: 0;
    }
    #label-about {
        font-size: 11px;
        margin-top: 25px;
    }
    #label-ad {
        font-size: 16px;
        font-weight: bold;
        text-align: left;
        padding-top: 29px;
        margin: 0;
    }
    #label-nav {
        font-size: 16px;
        padding-top: 15px;
        float: left;
        width: 86px;
        display: inline-block;
    }
    
    #header {
        background: #f5f5f7;
        margin: 0;
        border-top: none;
        height: 60px;
        width: 320px;
    }
    
    .blue {
        color: #167efb;
    }

</style>

从 iOS 6 开始，苹果在 iPhone 上引入了 IDFA 和 Limit Ad Tracking，用以更好地保护用户隐私。在最近 beta 的 iOS 10 上，事情又有了新变化。从 iOS 10 开始，只要用户开启了 Limit Ad Tracking，系统将干脆不再提供真正的 IDFA，而是代之以一串 00000000-0000-0000-0000-000000000000，从而确保 IDFA 不再被滥用。

[来源: https://fpf.org/2016/08/02/ios-10-feature-stronger-limit-ad-tracking/](https://fpf.org/2016/08/02/ios-10-feature-stronger-limit-ad-tracking/)

IDFA 用于取代之前的 UDID，开发者不再被允许获取手机的 UDID，识别用户只能通过新的 IDFA（Identifier for Advertising）。生成算法也不一样，UDID 是 SHA1(serial + ECID/IMEI + wifiMac + bluetoothMac)，而 IDFA 是一个由 iPhone 随机生成的匿名 ID，广告网络依然可以用其定位用户，投送定制广告，就像对待 Cookie 一样，只不过 Cookie 不跨域，而 IDFA 是可以跨 App 的，但用户也可以自己手动重置，达到类似清除 Cookie 的目的。

Limit Ad Tracking 则更进一步，它允许用户通过开启这一功能，去限制开发者和广告网络对自己 IDFA 的应用。它是一个君子协定，跟 DNT 一样，开启之后，会发送一个标记给采集者，告诉他们，依照苹果开发者条款，你们可以继续使用这一 IDFA 去做一些用户统计、转化跟踪、广告欺诈分析等，但不能再去投送定向广告。可能有的完全遵守，有的部分遵守，比如不再投送行为或兴趣定向广告，但继续做地域定向，继续做一些跨设备用户识别（我猜是搭配一些强帐号体系）。

而 iOS 带来的更为严格的 Limit Ad Tracking 机制，从根本上避免了类似 DNT 的尴尬。对于数据采集和用户识别来说，确实更为艰难了，但国内现今的各项 Web、Mobile 服务均要求手机注册，近乎实名，除了配合审查，估计也是早看不上 Cookie 类的东西。像运营商的劫持广告，对待用户几如韭菜，哪里谈得上隐私和尊重，最终还得靠利益链条内的冲突环节来缓和问题。