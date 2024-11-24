---
layout: post
title: "La 🌶️"
date: 2024-11-24 16:00 +0100
---

Raycast 很早就内置了 AI 功能，而 Alfred 直到 2024 年 3 月发布的 5.5 版本才增加 Text View 并推出自己的 [ChatGPT 插件](https://github.com/alfredapp/openai-workflow/)。而且，顾名思义，它仅支持 ChatGPT，对话场景单一，配置固定，提示词调整也很不方便。但是现实情况是，我们往往会用到多个 API 的多个模型，并且即便使用同一个模型去完成不同任务时，往往也会搭配不同的模型参数。

以自身举例，我需要常用大语言模型的场景有4个，分别是翻译、改写、解释、和随便问问，此外还有一些临时任务比如帮忙写注释，写 Git 提交信息，分析词汇和短语，问题分析及思考等等。这些场景任务目的不同，对应的提示词和模型参数也不应该一样。另外，模型很多，提供模型的API平台也很多，我希望对比着用，找出效果、速度、价格搭配上的最佳组合。因此，我期望的这个 Alfred AI Workflow 既要兼容不同场景，还要支持添改提示词、参数、及API信息等。

基于以上个人需求，我对官方 ChatGPT 插件进行了魔改，重命名为 La 🌶️。具体改动如下：

1. 支持其它大语言模型，只要接口规范兼容 OpenAI 即可
2. 支持自定义场景自定义提示词
3. 删除 AI 画图功能

![la](/files/2024/11/la.png)

如上图所示，大改动有三处：一、最底下的 Text View，此处调用的 JXA 脚本完全重写了，当然主要是 AI 的功劳，我只是 AI 的 Copilot，提需求，做测试；二、Text View 正上方的的小模块，不再依赖 Alfred 环境变量，完全变成 JSON 配置解析器；三、再往上的这些小功能，比如翻译、Git Commit、Eli5、改写等，原则上可以随意添加，配置好过程把最终输入指向第二条的小模块即可。

## 使用

一、通过关键词（默认是 ask）触发。特别适合零散的小提问；二、通过快捷键拉起。特别适合一些提示词固定又常用的任务；三、Universal Actions，比如翻译，一般是选中一段文字，按下 Universal Actions 快捷键，选中「翻译」，如果有需要则再指定目标语言，然后等待结果。

当然，你也完全可以为所有任务都设置不同关键词、快捷键、甚至 Universal Actions，但没必要，徒增记忆负担，但需定制最好。

![la-ask](/files/2024/11/la-ask.png)

![la-hotkey](/files/2024/11/la-hotkey.png)

Unversal Actions 是真好用，不知道 Raycast 现在有类似功能了没。

![la-universalaction](/files/2024/11/la-universalaction.png)

## 配置

$$ Magic 🔥 = Wand 🥢 + Gesture 🖖 + Spell 📖 $$

一个应用场景就是一个魔法（Magic），一个魔法由三部分组成，即：魔杖（Wand）、手势（Gesture）、和咒语（Spell），它们依次分别对应：API 平台（Provider）、模型及参数（Model & Parameters）、系统提示词（System Prompts）。用户消息则取决于使用场景，可以是选中的文字，也可以是键盘输入，是获取的一部分，不用放在配置里。

![la-config](/files/2024/11/la-config.png)

如上图，魔法配方是一个 JSON 文件，可以存放在任意目录，比如 `~/.config/alfred/la.json`。在这个配置里，我们为不同的任务调制不同的配方，配方可以随意发挥，最终，一次调用就是一次完整的施法过程。

我们先去对角巷采购魔杖。如下图所示，我们需要为每个商店起个名字，然后填上对应的 API 地址和 API Key。Azure 等云厂商往往需要特别对待，我们可以先把 `resource` 及 `deployment` 等特殊字段标记出来，放到手势配方里去统一替换，这样可以保持此处的整洁和固定。

![la-wands](/files/2024/11/la-wands.png)

接下来我们去霍格沃茨城堡五楼，在图书馆里按需抄录一些常用咒语。示例如下图，我常备的有翻译、改写、问询、解答等。部分咒语在使用时需要指向目标，我们可以先用 `context_placeholder` 做个标记，在施法时在明确替换。

比如翻译场景，我常用的有「中英互翻」和「中西互翻」，虽然目的都是翻译，但至少涉及三种语言，且并不总是要翻译成中文，源语言和目标语言是不固定的。所以我们在此处留好标记，在 Workflow 内设计一个停顿，默认翻译成中文，有其它指向，在停顿处写下目标语言，在下一个步骤用这个目标语言替换掉上面的标记即可。

![la-spells](/files/2024/11/la-spells.png)

接下来是时候躲到万应室专心调制手势配方了。内容并不复杂，给每个手势起个名字，指定一个模型，然后配置模型超参数，但超参数大部分时候配置一个 `temperature` 即可。比如「翻译魔法」，需要的温度往往非常低，我们可以将 `temperature` 设置为 0。而「随意问答」时，为了确保它不会过于死板，我们可以保持这个值在 1 附近。

对于 Azure OpenAI 之流，我们在这里把 `resource` 和 `deployment` 等标记补完，有多个资源多个部署，设计多个手势配方就好。

![la-gestures](/files/2024/11/la-gestures.png)

最后我们再把这些部件装配起来。比如翻译，我们使用架设在 Groq 平台上的 Llama 模型，效果好，速度快，还免费。再比如随意问，我们用 xAI 新放出的 grok，有 25 刀的额度，正好体验一下它有什么独到之处。

![la-magics](/files/2024/11/la-magics.png)

如上所述，这个配置是随意扩展的，因此你可以根据自身需求发挥想象，挑选你中意的魔杖，设计你独有的手势，吟唱你编造的咒语，并把它们组合成你的魔法。

```json
{
  "kill_harry_potter": {
    "wand": "acacia",
    "gesture": "↑↓←←→→ba",
    "spell": "avada_kedavra"
  }
}
```

在 Workflow 界面里，你需要创建一个子工作流，它可以通过热键、关键词、Universal Action 等多种方式拉起，结尾指向 `chat_magic` 即可，它会接管施法之后的工作。

![la-tom](/files/2024/11/la-tom.png)

中间特别要注意，别忘了配置 `intent` 项，这是 Workflow 程序和我们魔法配方之间的桥梁。

![la-tom-intent](/files/2024/11/la-tom-intent.png)

## 用例范例

![la-translate-flow](/files/2024/11/la-translate-flow.png)

以上面提到的翻译为例，我现在不需要考虑如何实现「从选中到翻译」的一整个 Alfred Workflow，只需要关注最前面的 UI 互动场景，即接受怎样的输入，要对输入做些什么处理，最后把整合完毕的输出信息指向 `chat_magic` 主模块即可。

所以我只需要创建一个小 Workflow 结点，最前面的 Input 按使用习惯设置两种触发方式：快捷键和 Universal Actions。往下，针对是否「开启新会话」做个分岔。如果是不想干的翻译，就走新会话，互不干扰，如果是一个话题或者一篇长文的陆续翻译，就走现有会话的累加，把这几次翻译放在一起。通过为 Universal Actions 增加修饰键就可以实现它。

再往下，因为翻译成中文的情况比较常用，在目标语言这里再做个分岔，默认中文，其他情况自行补充。为了避免翻译成中文时每次还弹出目标语言框，我又设置了不同的起始快捷键，实现一键直达的效果。

![la-translate-action](/files/2024/11/la-translate-action.png)

![la-translate-lang](/files/2024/11/la-translate-lang.png)

![la-translate-result](/files/2024/11/la-translate-result.png)

![la-translate-result-2](/files/2024/11/la-translate-result-2.png)
