[kanban——自定义任务流程【玩转Obsidian的保姆级教程】 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/409234073)
## 1.高效编辑
- advance table ：表格编辑，自动化文本
- pangu：自动为中英文字符添加空格
- outliner：自动排序，适用于列表编辑
- paste url into selection：快速为文字添加超链

## 2.写日记

- calendar：提供日历视图，快速创建日记、周报

## 3.任务管理

- checklist：跨文件整合任务列表（#same tag）

![](https://i0.hdslb.com/bfs/note/80b06312748bd5e14de5d5f2c7adc91fe65ba336.jpg@680w_!web-note.webp)

- kanban:看板

![](https://i0.hdslb.com/bfs/note/66aaf9d8f80639ba44175fdd0d8e4c8057cab7e0.jpg@680w_!web-note.webp)

  

## 4.展示类

- advanced slides:简易PPT，支持白板功能

![](https://i0.hdslb.com/bfs/note/02085149348db6daca2ee7340ba114e737cce74e.jpg@680w_!web-note.webp)

- obsidian excalidraw：手绘风格绘图软件（但官方正在开发类似）

## 5.极客工具

- obsidian git
# **▍**用法 1、日记随笔（Daily Note）

Obsidian 默认不开启 Daily Note 功能，Logseq 或 RR 用户可能会很不习惯，而对另一些没有接触过 Daily Note 概念的用户来说，也很有必要先介绍一下它的应用场景和配置方法。

## 什么时候记 Daily Note

1. 当你想写日记的时候，它就是一个支持 Markdown 的日记本
    
2. 当你想快速记录灵感的时候，你不用也不该纠结应该放在哪个文件夹，就像传统纸笔，翻开就能写，就像摄影，拍到比拍好更重要。**而使用传统的笔记软件，你总要先找到（或建立）一个合适的文件夹用来存放**，但灵感和冲动往往消失在这个麻烦的时候。
    

## 配置方法

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHxZ3oVJ9N2ExjgI42Aty1Wx0QkRUS89kC9kb1R4qIyPSQibUWRiay6eaA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 日记模板

一般来说我更习惯于用无序列表来让记录这件事更纯粹一点，模板对我来说会有填空的压力。但你也可以事先设置诸如晨间日记、每日工作等模板。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHbPA745liaQEE6qdZcRGzicggBJ3qayJqtDImGDxO8vaBdz5RJGb4touA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

设置好模板后，**使用快捷键**创建日记文档，就能自动调用这个模板，就像下图这样子：

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHXD31uziaXoGA7a6fRDNF2XYVF55MkPFBMqcNkPAghTxIQY8FUG6gsDg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

当然你也可以让 Obsidian 在启动后，自动载入当天的日记页面，在【设置 - 插件选项 - 日记】中你可以找到这个选项。  

## 日记进阶 01：双链入门

之所以能够随心所欲、不用分类地在 Daily Note 中记录一切内容，是因为有双向链接这个功能的托底。

**①** 点击每一个被 `[[]]` 框住的关键词，都会自动创建一个以这个关键词为名的文档，然后进入到这个文档中。比如在下图中我就通过这种方式创建了一个名为 `Notion` 的文档。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHztsrmg0faDeeI0pdaE4JrBrs2gObEuaicGWAEwg7WxW2u17P18EC30A/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

**②** 打开这个文档的反向链接面板，它会在笔记库内搜索**这个关键词曾经出现过的位置**

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHWI2JURgkDE9JKJ1BiasicQdnqk2xLibWD6SwAQd2YRHsDsoniavtFzOUYg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

所以我们用 Daily Note 记录那些不成主题的碎片信息，直到你有明确的专项题目可以拓展，再通过反链面板去回溯过去在 Daily Note 中留下的碎片记录，有时候靠那不经意的一句吐槽都能带来无穷的启发。  

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHGuymelzEweZ8pYibFOBpyOgRWLGYlA3XTV2yNQPgD8iaZcSafEOBMaLw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 日记进阶 02：初级任务管理

使用 Task 插件能用非常简单的语法，将分散于各篇日记的任务集中于一处管理，请在安装并启用 Task 插件后，执行如下操作

1. 在【设置 - 快捷键】中，搜索 `Tasks`，并为 `Task: Create or edit task` 创建一个快捷键命令，例如 `Ctrl+T`
    
2. 使用快捷键 `Ctrl+T` 唤出 Task 任务面板，然后在下图的自定义选项设置你的任务
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlH3f2rAKRZvyU7DzRI3gfPjjWbaopTNFxORzp0QIeTdhkgAzeUADTqicw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

设置完毕后，你就可使用非常丰富的选项，来制定你的个性化任务面板，下面这段最简单的代码，能让你筛选出你**整个笔记库内所有未完成的任务。**

````
```tasks 
````

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHxMMKaDE1ibaSa2en1tGO4P67iau07DJ43sBhBpqUpBlOYf8Ieh0TOzpA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)  

你还可以参考开发者文档（https://schemar.github.io/obsidian-tasks/getting-started/），制定更加个性化的任务面板，也可简单参考我下图中的命令。  

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHPYmibXj8f6r6jljFeTTOrxLqSQLiaev00nPxKfnNqTVgRuZEtvrPWECw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

  

**▍**用法 2、日程与任务进阶管理

## Day Planner 插件

Day Planner 与前文提到的 Tasks ，都能对笔记库中的 To-do 内容进行集中管理，Tasks 插件会更自由灵活，它能够扫描到笔记库内**任意地方**的 To-do 内容；而 **Day Planner 则只能扫描特定的文件夹**，但换来了更加精细、更加有趣的日程/任务管理方式。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHP65UL6HMK9Cwlyu0tTTPpzSDMmwH5EFAltUDt6NFBnLC43jFFibNMNg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 文档顶部自动生成甘特图
    
- 右侧自动生成时间线
    
- 底部右下角有全局的任务提示
    
- 当任务到点时，还能弹出系统级的通知提醒
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHSjmgmfhAoSGqrVqyiaYsmJibSdvMbLeO6al1yWIQ8No3AP033x1x7fSQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 文档模式 File mode

安装完 Day Planner 插件后

1. 会默认开启 `File mode` 功能
    
2. 会在笔记列表根目录中自动创建名为 `Day Planner` 的文件夹
    
3. 在 `Day Planner` 文件夹内，每天都会自动创建一个新的日程文件
    
4. 你需要用下列语法格式来书写你的日程，才能被插件正确识别
    

`## 任务标题-[]07:00 任务`

- 你也可以使用 Ctrl+Enter 来快速创建 Checkbox
    
- 时间需要 24 小时制，1:00 应写成 01:00
    
- 时间与任务描述之间需要空格
    

5. 如果右侧时间线没有正确显示，请使用下列操作：

- 使用快捷键 Ctrl+P 唤出命令面板
    
- 搜索关键词 `show` ，然后选择 `Show the Day Planner Timeline`
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlH4xcvo4rlQTlRDdzjY8Alzicq6MjxD3dia1u7lp6sqRwoqbjhNqXcX5qw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 命令模式 Command mode

在插件设置中，将 File mode 切换到 Command mode，**能够让你在任意一页文档中使用 Day Planner 插件**，而无需创建额外的 Day Planner 文件夹。任务书写语法与 `File mode` 一样，但你还需要执行下面的操作

1. 使用快捷键 Ctrl+P 唤出命令面板
    
2. 搜索关键词 `link` ，然后选择 `Link todays' Day Planner to the current note`
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHIfypwLvtJJfHGb6bIMiblic2Dyuw4NV97l2jw5giczn3waAbumKa8KeqQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## Kanban 插件

你可以像 Trello、像 Teambition、或者像 Notion 中的 `Board 面板` 一样，去使用 Kanban 插件，它在常规看板的基础上，同样支持了双向链接功能，完整介绍你可以参考开发者这份文档。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHuXG4aawnRl4CbFDGiboSeFK6kPLhf80DVKMu1oxYJQ1pjcLbXgcNn3A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**创建看板的两种方式**

- 右键点击任意一个文件夹， 选择 `新看板`
    
- Ctrl+P 唤出命令面板，搜索 `Kanban` ，并选择 `创建新看板`
    

**启用看板复选框**

- 【设置 - 插件选项 - Kanban】中，开启 `展示卡片复选框` 功能
    

**看板与双链联动**

**①** 为看板卡片添加时间，例如 `[[2022-03-24]]`，当那天到来时，你就能在 24 号日记的**反向链接面板**看到这张卡片

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHQSZ3Zsx1yITPhCR6hq9VgMIpMP2b0lXfpbb1CAdzLES3DFyqYant2w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**②** 每一张卡片都能创建为一份文档， 就像 Notion 中的 `/page 命令` ，你只需要点击 `从卡片新建笔记` 即可

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHbMtHibYOOSsL2Aq35P5plFxBmeElomosBFOKiaicyMCKzatDEicaibApUwg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

# **▍**用法 3、工作记录 & 报告复盘

工作最让我感到厌烦的，不是工作本身具有难度，而是每周、甚至每天都得形式化地写又臭又长、根本没有人看的工作报告，于是我用下面两个插件来尽可能地减轻这种痛苦：用 `Tamplater 插件` 来创建自动化的模板，以及使用 `Dataview 插件` 来让 Obsidian 拥有高级检索能力。

## Templater 插件

这个插件能让你在不同文件夹创建文档时，自动调用不同的模板。

例如在 `日报文件夹` 创建文档则调用 `日报模板`，在 `会议文件夹` 则调用 `会议记录模板`，它比 Notion 的 `Template button` 更便捷，比 Logseq 的 `Template` 的自定义程度更高，比印象笔记要加载半个世纪的模板库更是不知快到哪里去了。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHja8Phuico2BhsfbdaBucIbDzbBgg902pr1fjjvnynDGlwibfFEkVCOMg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 插件配置

1. 你需要先建立一个专门用来存放模板的模板文件夹，并且提前写好你所需要的模板
    
2. 打开【设置 - 插件选项 - Templater】
    

- 在 `Template folder location` 将路径指向模板文件夹
    
- 打开 `Trigger Templater on new file creation` 这个选项
    
- 然后在 `Add New` 那一栏，将文件夹与模板一一对应。如下图所示，左边是文件夹，右边是模板文件。
    

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHJx6U6DSP6hGUfsW78wgELR5QEfpZFV2y6Y0fAE3VZic3mJfV0ibInMrw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

5. 如此一来，当我们在 `会议记录` 文件夹新建文档，它创建的就不是空白文档，而是加载了模板的文档了。
    

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHRTMJzj4GZXam86CInpriapOkaABwPNVTVjIfal5NsUnvp0VWgbytKEg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

你可能注意到了，上图调用模板生成文档时，还自动生成了时间 `2022-03-22`，原因是我在模板中使用了 Templater 提供的时间变量，用法类似 Excel 中的 =today()函数

```
今天：<% tp.date.now("Do MMMM YYYY") %>
```

更多变量你可以参考阅读开发者的这份文档（https://silentvoid13.github.io/Templater/introduction.html），善用变量，你还可以在每天生成的日报中显示 上一页 和 下一页 的效果。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHJ6LKdZpuABA2AmdnbVtxDniamO8PJdTNf7CNMQeuqPicXvfwIYQD20hA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

这时你可能又会注意到，使用 Templater 插件创建的文档都叫 `未命名`，那么有没有办法在创建文档的时候就自动使用当天的日期作为文档标题呢？答案是有的，你需要的那个插件叫 QuickAdd。

## QuickAdd 插件

这又是一个非常强大的插件， 不过今天我们只介绍它的四个功能之一：Template。它有两个特性，分别是为 Templater 插件的模板创建一个快捷键，而无需像之前那样通过右键文件夹来创建文档。另一个特性，则是让 Templater 插件创建文档时，让文档标题能以特定格式来自动生成，例如我们现在需要的「日期」。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHWTiclQrymCu4txKmOADEjUxox0J1NyfAKMXaeicpgGvukCcGzH93tgnA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

### 配置

1. 为 `创建日报` 添加一个 QuickAdd 动作

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHDlxXsIj3UibWWgY02PTkrkxRewamDK87uIQc6e7p0h5GA6zdWcd3M4A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

2. 先点击小闪电，这样才能在命令面板（Ctrl+P）中直接搜索到；再点击小齿轮， 进入详细配置界面

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHpKg0fGlCvXqNcYeQxT8m67PdDGXGk3KQYqBcKZqRqFHhCRsiaB308Ew/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

在这里我仅介绍了 QuickAdd 可能不到十分之一的功能用法，如果你乐于折腾，可以继续阅读这份开发者文档。

## Dataview 插件

假设现在到了周五临近下班的时间，我们需要**快速将 5 篇日报和 3 场会议记录整合成一篇周报**，如果是以前使用传统文档记录，我们可能需要**分别打开 8 个文件**，来回切换窗口，然后慢慢地将它们复制粘贴在一起，这真是一件既费心又烦人的事。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHKkUnx2keOkdzQCia9iaRgzGVicT8SSDzH6iaBz8mbBy6Nbd4o77SGTOnCA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

而同样的事情如果发生在 Obsidian 中，事情则会变得轻松不少。

如下图所示，当我在周报文件夹创建文档，触发了 Templater 的模板，而这个模板则用了 Dataview 语法进行了配置。我利用 Dataview 插件，自动汇总了过去一周的所有日报和会议记录：

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHTWKKu6Sps0xjdAMz2oWjW8jv8uF7icZLO7WmUovTuOamvbwekwYInDg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

你可以 Ctrl+ 鼠标左键点击 ，来打开同一份文档。左侧窗口使用 Ctrl+E 进入阅读视图，鼠标悬停在日报标题上方，通过弹出的小窗获取周报所需要的信息，然后就可以在右侧编辑视图下快速输入了。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHSiav2DcRXk3ibv1jwEpCnMtsk6NmxIk791ibAk3encsLRuv02UXMB1Pqw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

### Dataview 配置方法

将下面这段代码放到 Templater 模板库中，然后命名为 `周报模板`

````
```dataview
````

然后再设置当 `在周报文件夹创建文档` ，则 `调用周报模板`  

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHd8JFbyC1fQQA1ZYnS4cFFYkISFgMJVVTYUmEKicLyGd2ickcPLb4kJibg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

但是你会发现，上面那段代码会汇总 `工作` 文件夹中的**所有文件**，因为我们还没添加过滤条件

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHfgQILU9vWXJK0xskAIYq40Z5YqNLG8VgNLQ2ibiciboc7KEI9JheJNIvw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

显然我们需要的只有那些创建时间在一周之内的日报和会议记录文档，所以我们还需要两个步骤

1. 为需要的文档打标签，分别是 `创建时间`、`日报`、以及 `会议记录`
    
2. 让 Dataview 筛选过滤这些标签
    

### 用 YAML 语言为文档打标签

你可以在日报最开头手动输入下面的内容，**注意是英文输入法下的冒号，并且冒号后面需要空格**

```
---
```

也可以用 Templater 插件在创建日报文档时自动插入

```
---
```

好了现在我们可以让 Dataview 插件正确过滤**创建时间在 7 日之内**，并且**包含日报标签**的文档了。

````
```dataview
````

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHcPG23EqIGv9USR1X4uBcuwRS444ExW6vFR3kQfDUWknu3Cs5BnHh9A/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

后面的内容我还讲反复提到这个插件，你可以自行阅读 Dataview 插件开发者文档，学习更多高端操作，也可以点击下面大图，参考 Dataview 的部分语法。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIpNflHic9dh6jjKrT40m3vxww7hFN1oEADf3eNWjljLNbic4Lv8QPKCAw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

# **▍**用法 4、读书批注

## Annotator 插件

Obsidian 原生支持 PDF 阅读，你只需要将 PDF 文件**拖到文件夹上**即可载入，而安装 `Annotator 插件` 除了能让 Obsidian **额外支持阅读 EPUB 格式**的文件，还能进行高亮、批注等操作。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHcKBZ1Jr4E3Cmv3ibzde9nJvzbicQFIuq5weuCibRuQlX5M3Rfs68tw4Ug/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

### 插件用法

1. 将你的 PDF 文件拖入 Obsidian 的文件夹上
    
2. 新建一个文档，然后在文档的最开头添加下面这样的 YAML 语法
    

```
---
```

例如，如果我要打开存放在`附件`文件夹中的 `基督山伯爵.pdf`，则正确的语法应该是  

```
---
```

然后 Ctrl+E 进入阅读视图，如果无法正确读取 PDF 文档，可以点击右上角的三个小点，然后再点击 `Annotate`

插件效果

1. 所有高亮和批注都会成为侧边栏中的卡片
    
2. 点击卡片即可跳转回 PDF 原文位置.
    
3. 你还可以为批注打上可被 Obsidian 识别的标签
    

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpISyYEibRPVcskY6kExRpOClqBfgw7EdKkDWWfuyw2YUCYc6FICQiaXSBA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

点击右上角的 Open as MD 将关闭批注模式，然后你就能看到，所有的高亮和批注都被搜集起来组成一篇文档了。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIu7opjAp98tGKylgXDG0YS9NX4Yn01YUiakpUQlrUES70hWaRyJ581QQ/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

Mindmap 插件

这个插件能够让文档的各级标题成为思维导图中的各个节点，并且导图中的各个节点也都是可以点击的。虽然导图的样式单一，且配色实在辣眼，但胜在简单易用。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHSvx4oN90bREnKfyNBwVThENrMyQd5jcdGoAx3npsOPx1Oz4delxvTw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

# **▍**用法 5、动态表格

这个用法本质上还是 Dataview 插件的另一种应用， 与前面的清单（list）不同，这次我们要绘制的是一张可以动态变化的表格（table）。假设我们为 4 本书分别写了 4 篇读后感，并在文档的最开头用 YAML 语法进行一点配置（打标签）

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHXSicicD7v1p1FPnYC7E680JIulFpuVHzszWcKmbpxHtB8DFwRbE0MvTg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

然后使用下面的 Dataview 语法进行配置

````
```dataview
````

翻译一下上面这段代码的意思  

```
table 定义的是 Dataview 的展现方式
```

生成这样的表格效果如下图所示

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIgRRYUoNXXicEaNH4gsBNoY0SmKAZnFVejYBQCLkF97LBIaXia3vemjKQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这个表格会动态更新，实时扫描整个笔记库，只要你新增一篇在 YAML 表头包含了过滤标签的文档，它就会自动在表格内增加一行。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHECcUXz3r85fFFpGYoNnNmTI7NkDe9bq8GSuS1tUcWIODHewZibxnCPw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

# **▍**用法 6、视频笔记

作为一名新人 UP 主，我会特别注意视频中弹幕激增的节点，然后去分析这个 UP 主说了哪些话来激发观众发送弹幕的热情，很多看似 UP 主不经意的一句话，其实背后都能找到很多运营的门道。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlH0LibibVJo9b1sJqibhAaDbZsSo3qLh2oSQib792rxNebGZx1IjzUH0cl8g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

B 站本身自带视频笔记功能，但只能说堪堪够用，回看笔记的入口很深，而且完全线上保存无法导出，好处是原生功能，打开即用无需折腾。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHuWvxypJajiaQSNiaiaWMprTBj3DbcSLGOyFQckhSnIYduje8sYb7NRDwA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

在 Obsidian 中，你可以这么操作

1. 复制粘贴视频网址到 Obsidian 中
    
2. **在阅读视图下**，才能在 Obsidian 内部打开视频链接
    
3. **在编辑视图下**，才能通过右上角的五角星截取时间点
    

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlH56uyuM8xEdgKJ1kUicywlHApWwCMgmCTv9y3icJF9BKhbIk3kibqlMHYw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

**案例：如何更有效地「骗」弹幕？**  

**![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIreaLvEAbeiadpbcjxjFWShqJiaK9NciahgJ9If0UlOsoqpYlLoHqujsog/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)**

如上图所示，我会使用 Media Extended 和 Media Extended Bilibili Plugin，将笔记和视频界面并列来学习其他 UP 主的视频。（如果这个插件无法正常启动，请先关闭 Annotator 这个插件，重启 Obsidian，再尝试启用。）

具体而言，通过内嵌的视频窗口，以及**点击即可回溯的时间节点**，能让我在复习这段话术时，更直观且更深刻地去学习这个 UP 主通过视频所能呈现出来的方方面面，**因为我不止要学习他说了什么，还要学习他是怎么说的**，学习他搭配了什么样的画面，语调的抑扬顿挫以及搭配的 BGM 节奏如何，而这种便捷的笔记+视频的呈现方式，是绝大多数只能单纯内嵌视频的软件所无法做到的。

例如，下面是我在看一个视频时记录的一些观察，供参考：

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd54znFTDbJibsfyvfo1JhOQPjjqtJ9bMmXD3Wl5JNq5E24X4hBBraWqGM8EGkyaibypKcJVSdm6Co4Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**▍**用法 7、数据复盘  

说实话，如果你有正经的数据分析工作，还是用正经的 Excel 或者其他专业软件来分析，此处仅用来展示 Obsidian 在数据呈现这块的可能性。

## 使用 Dataview 来聚合数据

以我的 B 站投稿为例，我将视频数据放在 YAML 表头，然后在正文记录运营相关的分析和复盘

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHGBCltEGEAdWWRg5OEesd5ahDd9ia8fmvF7whQ4XkCO5J9ibr8nvaqleQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

然后就像前面提到的阅读清单，这张表格也是可以实时动态更新的。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHHWGkU2XHEmVPHicbnmGvM3NTdgl86s7mBXBZdCTrequhSmuVVIwGHJA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIFhyRzf0W9xNIbp7lysQ6j18tcYBErVUuGrzujLFKWoZISWDa2qDzzQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 使用 Charts 让数据可视化

这个插件能够让你手动插入如下图所示的五种图表

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHRTibYXfqxc88tk1EX7WEeJKZzCG5bickP48lHro9yug7cW7YwAmv8W7w/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

安装完插件后，使用 Ctrl+P 唤出命令菜单，然后搜索 charts 关键词，选择 Insert new Chart 即可，然后剩下的就是手动输入数据的基本操作了。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHTK76O6ehPBQE4X7BWRpK0ibibmxicuQTcMQjNqFTk9Yq2jHjome5ILCag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

# **▍**用法 8、编年史迹

## Timelines 插件

你可以用这个插件做一份投稿时间轴，也可以做一份游记时间轴，其实除了好看没有太多额外用处，但因为它能做，所以我就做啦。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHn8aEyXIbia42K9GLCJCmZZTwzAQkY6XDmVZtdb7f1SFJeC6NFpxuACA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd6h4TuzuRJgb4ulBiaBRGDpIuEJxzWOuicCibMtj0CNc2xvXMGH2FDAaVbJzJlQjKFqJ4jGSOrTqSymA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

除了竖轴，还有横轴可以选择

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHytOFV9DH2QGM9OQRY8CPWLN2e4XmWib7BStU8472gnxX7UZlYaylILA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

## Timelines 插件配置方法

乍一看，配置时间轴的代码非常简单，只需要像下面这样，筛选出所有包含影评标签的文章即可

````
```timeline
````

![图片](https://mmbiz.qpic.cn/mmbiz_gif/oiabfUBO7nd798sY99oamUhGqdrE6KAlHX6Pr61Eah0NfBtzcLibnMzFEecVpmDpR8Nvct72EtT9vSlUb5lmV7Sg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

但这样做的前提是，我们需要在被筛选的文章内，先进行一点点配置。

首先必不可少的还是要先用 YAML 语言打上几个标签，其中 `timeline` 标签是必须的，另一个标签则可由你自定义。你可能注意到了，我们可以在 `[]` 内一次性输入多个标签。

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlHENibJcI7iayMkMicpRRwdo3B8tbc5SPA6CmnGKwPGaDMN37ML9eJP0iciaQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

然后还需要在正文中插入一段代码，来定义时间轴中的文章封面、标题、时间等。

```
<span 
```

如果你直接复制上面的代码，**记得自行修改一下单引号内的内容**。其中我们需要修改的有 date 、title 、以及 img 这三项，否则时间轴的时间、标题和封面将无法正确显示。  

![图片](https://mmbiz.qpic.cn/mmbiz_png/oiabfUBO7nd798sY99oamUhGqdrE6KAlH0mOyQ15AZPZIbTb01qYcqX0ibiba5Qv1caWQQibdkibfI3licnXwb2cMOEA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

更多关于 Timelines 的教程，可以阅读插件作者的这份文档（https://github.com/Darakah/obsidian-timelines）