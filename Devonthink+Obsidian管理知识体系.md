# Devonthink+Obsidian管理知识体系
### 背景
最近为了写毕设论文，看了很多论文和网页资料，看的时候经常会遇到一个知识点觉得应该记录下来，或者一个网页觉得可以用到毕设里，或者就是有一些灵感。另外，在写法语毕设的时候，还希望在正文中能看到这段文字的来源。对于第一个需求，我之前是用印象笔记的桌面助手添加卡片，浏览器插件剪藏网页，但是后来印象笔记的广告让我不堪其扰，剪藏网页也有很多格式混乱，图片不显示的情况。

最重要的是我的卡片和网页放进去后，因为彼此之间没有联系，几乎不会再拿出来看；对于第二个需求，我试图用Notion来布局我的素材和文字，但是布局起来非常非常麻烦。

我找了很久，先是找怎样把网页原封不动的保存下来，各种稍后读软件raindrop.io，cubox...最终发现DEvonthink的web archive完美的实现了这个功能。随后了解了双链笔记，感觉双链可以保持卡片之间的关联性，也能让我在论文中引用资源，于是尝试了logseq，obsidian后，最终选择了obsidian（实在没学会怎么用logseq）

### 配合使用

在Devonthink中通过“连接库和文件“将Obsidian的vault链接进去，在vault同级文件夹下建立网页剪藏文件夹和资源文件夹也都链接到DEvonthink中。

平时添加新的卡片通过Devonthink在菜单栏上的Sorter进行，Devonthink可以手动把文档中#后面的内容识别为标签然后添加标签。（没办法添加卡片时自动识别很烦！）

### 用devonthink原因如下
- 菜单栏sorter添加卡片非常方便
- 剪藏safari网页为webarchive可以保留全部网页格式，还能做标注，还能搜索
- devonthink的标签系统可以把关于一个主题不同种类的资料全部整理到一起
- devonthink中也有双链（wiki link）功能，在obsidian里添加的链接在devonthink中也能使用
- 资源保存在本地
- 一次性买断

### 用Obsidian原因如下
- vault可以和devonthink联动使用，可以通过devonthink添加卡片到vault里
- obsidian可以链接devonthink中的资源，插件Devonlink完美实现了这个功能
- 关系图谱看起来很炫酷

### zotero联动
zotero加入zotfile插件后，可以把pdf都放到一个文件夹下面，把这个文件夹链接到devonthink里面，就可以用tag非常方便的管理论文以及与该论文相关的网页存档，卡片，用pdf expert进行的标注也可以保留下来，从此彻底从endnote转移到了zotero


### 有待提升
1. 如何与marginnote联动？我把需要细看的论文都放到了marginnote里
2. 如何在devonthink添加卡片时候自动导入tag？
3. 感觉纯用标签管理编程笔记仓库以后会非常混乱...前端，安卓，python，java这种和一些主题研究tag混在一起，但是我没有想到很好的组织obsidian文件夹和剪藏/资料文件夹的办法，我希望在obsidian仓库里只有我自己写的东西，另外，每次写完一张卡片思考应该放到哪个文件夹下面也很烦...所以，我打算就先这样
4. obsidian不同vault之间设置的同步？我根据三个主题建立了三个库，每次都要重新配置一遍（比如obsidian-git）很烦

#工具 