---
layout: post
title: "📚 Español-Chino：macOS 西班牙语-中文词典"
date: 2026-06-09 15:23 +0200
redirect_from:
    - /macos-spanish-dictionaries
---

Español-Chino 做的事情很直接：把西汉词典装进 macOS 自带的词典系统。装好之后，你可以在「词典」App 里查，也可以在 Safari、Mail、备忘录等支持系统查询的 App 里选中西班牙语词语，右键或三指轻点，直接看到西中释义、例句、短语和变位。

它不是另一个需要打开的查词工具，而是 macOS 里随手可用的一部西班牙语-中文词典，原生体验。

![查词 preferir](/files/2026/escn-preferir.webp)

---

## 日常怎么用

**查西班牙语单词**

输入一个西班牙语词，词条会按词性组织释义，并显示西中对照例句。版式尽量贴近 macOS 内置词典：先让你快速确认意思，再往下看例句、短语或变位。

**用拼音反查中文**

有时你知道中文意思，却不想切中文输入法，或者一时想不起对应的西语词。直接输入拼音即可检索，例如 `shuo#`、`biao#`。

![拼音反查 xihuan:](/files/2026/escn-xihuan.webp)

**直接查短语**

短语不再只是藏在某个词头下面的附属内容。输入 `echar una mano` 这类完整短语，可以直接命中对应词条位置；带 `~` 的模式短语也会展开成可搜索的形式，减少“知道短语却找不到入口”的情况。

![查短语 echar una mano](/files/2026/escn-la-vida.webp)

**查动词和变位**

动词词条内置完整变位表，按 RAE/DLE 版式照 Indicativo、Subjuntivo、Imperativo 分组，简单时态和复合时态分开显示。不规则动词的同义变位形式会压缩合并，例如 `contrad(ec)iré`，窄面板里也更容易读。

搜索时也不用只记不定式。输入变位形式，macOS 可以通过索引找回原动词；如果想直接跳到变位表，输入动词原形加冒号，例如 `hablar:`。

![查动词变位 hablar:](/files/2026/escn-hablar-conjugacion.webp)

**查性数和复数形式**

很多查词失败发生在形态变化上：你看到的是阴性、复数或名词复数，词典词头却是阳性单数或单数。Español-Chino 为这些常见形式补了检索索引：搜 `pesada` 可找到 `pesado, da`，搜 `actores` 可落到 `actor`，搜 `piernas` 也能命中 `pierna`。

**看用法提示**

词条里的用法标注经过统一整理，例如 `Ú. t. c. prnl.`（也用作代动词）、`U. m. en pl.`（多用于复数）。原始注释中附带的中文语义说明也会保留。用法提示默认隐藏，需要时可在偏好设置中打开。

**查内置参考**

词典内置五张略语表（词性、方言地名、外来语种、语义、学科行业）和一份体例说明。遇到不认识的缩写，在搜索框输入 `略语表 1`、`略语表 2` 或 `体例说明`，不用离开词典再找外部说明。

![使用说明](/files/2026/escn-fb-matter.webp)

---

## 词条怎么读

词条顶部有快速导航，例如：

> tr. · intr. · prnl. · phr · Conj

- **tr. / intr. / prnl.**：及物、不及物、代动词，点击可跳到对应义项。
- **phr**：短语区，集中列出常用搭配、固定表达和例证。
- **Conj**：变位区，包含完整时态人称表格。

词条内还可能出现 〔Chile〕、〔Amér.〕等地域标记，表示该用法属于特定方言区。你可以把顶部导航当成词条目录：想看基本释义就停在前面，想看短语或变位就直接跳过去。

---

## 系统体验

**跟随系统外观**

亮色、暗色模式会自动适配，不需要单独切换主题。

**右键直接查**

在 Safari、Mail、备忘录等原生 App 中选中西班牙语单词，右键或三指轻点，选择“查询”，词典面板会直接出现在当前上下文里。

![light mode](/files/2026/escn-lookup-light.webp)

![dark mode](/files/2026/escn-lookup-dark.webp)

**偏好设置**

打开「词典」App，按 Cmd+逗号进入偏好设置。Español-Chino 支持独立开关例句、用法说明、变位和短语显示；如果你只想要更轻的释义视图，也可以把暂时不需要的部分关掉。

![偏好设置](/files/2026/escn-settings.webp)

---

## 背后做了什么

如果你只是想装来查词，上面已经够了。下面是给在意实现细节的读者看的。

**数据来源**

@winn 在 forum.freemdict.com 发的[这个帖子](https://forum.freemdict.com/t/topic/3622/42)。词典内容偏老，数据结构上有不少地方要修复。

**四阶段构建流水线**

整个工程按 Convert → Preprocess → Refine → Package 四段执行。转换、结构清洗、语义整理和打包各自独立，便于定位问题，也避免把一次性处理逻辑混进最终展示层。

**索引覆盖面系统扩展**

短语、性数变体、名词复数、动词变位形式都单独建了索引，不只依赖 macOS 默认的形态分析。名词复数回填会结合词干还原和语料中的单复数关系，尽量让 `piernas` 这类实际阅读中出现的形式回到可靠词头。

**用法提示结构化**

源数据中的 `<us>` 用法提示经过多轮规范化，覆盖常见缩写变体和组合形式，并把中文或自由文本说明作为结构化属性保留下来。这样既能统一显示，也不丢掉原词典里的语义信息。

**去重和重定向有保护**

构建过程会合并冗余条目、清理重复索引，但不会为了压缩体积机械删除内容。形态信息、短语锚点和变位入口会被优先保护，避免出现查得到却跳错、或者合并后丢义项的问题。

**参考资料内置**

略语表和体例说明作为词典条目打包在内部，不做外链。查词、看缩写、回到正文都留在同一个系统面板里完成。

---

## 安装

1. 下载 `Español-Chino.dmg`。内附说明 `README`，可选中按空格预览或双击打开。
2. 打开后将 `Español-Chino.dictionary` 拖入 `Dictionaries` 文件夹。
3. 打开「词典」App，按 Cmd+逗号进入偏好设置。
4. 勾选 Español-Chino。

![安装文件预览](/files/2026/escn-dmg-20260529.webp)

---

## 下载

- [OneDrive](https://1drv.ms/f/c/aa6edbb0266ff077/IgDkO0c-StvGQ6rB_7malpWHAVwBKNruyGPn7_UGTdgwhkM)
- [百度网盘](https://pan.baidu.com/s/18oHUVEYC5DPcb-KgmOemkQ?pwd=8e8v)
- [夸克网盘](https://pan.quark.cn/s/3f5f1a161316)
- sha256: 2f5c567c5b84b41229021f6f8c4b094ad9d3ea517f32f1c5cdbac6910b8a979d
- 加油 **¡Tú puedes!**

<style>
details.as-li {
  margin-top: -1.5em;
}
details.as-li > summary {
  list-style: none;
  cursor: pointer;
}
details.as-li > summary::-webkit-details-marker {
  display: none;
}
details.as-li > summary::before {
  content: "";
  display: inline-block;
  width: 0.4em;
  height: 0.4em;
  margin-left: 0.1em;
  margin-right: 1.3em;
  border-radius: 50%;
  background: currentColor;
}
details.as-li > .details-content {
  margin-left: 1.4em;
}
</style>

<details class="as-li">
  <summary>开心？请杯 ☕️ 就好。</summary>

  <figure class="feel_good details-content">
    <img src="/files/2026/cafe.webp" />
    <figcaption>Just for fun, 丰俭由君。</figcaption>
  </figure>
</details>

---

Alfred 用户？[Lexica](https://placeless.net/blog/lexica) 是一个 Alfred Workflow。它调用 macOS 内置 Dictionary.app 里的系统词典，把查询结果显示在 Alfred Text View 中。

![查词 manzana](/files/2026/lexica_es_manzana.webp)
