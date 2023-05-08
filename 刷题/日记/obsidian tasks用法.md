
**Tasks**是一款可进行任务添加，设定任务日期，快速汇总查询任务的插件，在Obsidian中通过对任务设置 截止时间、执行时间、计划时间、循环周期等，便于进行项目管理。

本篇将主要介绍Tasks这款插件的使用方法，和一下参数设置。  

使用的版本为 1.4.1  

## **添加及完成任务**

-   **添加任务**

-   在命令面板输入Tasks，选择`Create or edit task`，添加并编辑一项任务
-   `Ctrl/Cmd+Enter` 按两下，新建任务 

-   **完成任务**

-   - 预览模式下，直接点击即可，会出现完成时间样式
-   - 编辑模式下，通过命令面板`Toggle Task done`会显示完成时间，否则就和普通完成形式一样。

![](https://pic3.zhimg.com/80/v2-36c167fb48d4fdc2739d5ae3a5304a96_720w.jpg)

  

**循环周期示例**

![](https://pic1.zhimg.com/80/v2-c64c39e5475e4951fe91b670d3650334_720w.jpg)

其中：

任务文字不支持跨行，注释仅支持行内注释，Task插件仅在.md文件中有效。

输入日期格式如果为`Monday`（星期一），而今天是星期二，则会默认是设定的昨天。如果想要输入下周一的话，使用具体日期或`next Monday`形式

![](https://pic4.zhimg.com/80/v2-ca5a2e746244631ef21ba2f158dea617_720w.webp)

  

## 任务汇总查询

通过设置代码块，可用来汇总查询所有的task任务项，例如下列代码格式：

````text
```tasks
not done
heading includes OB插件
path includes template
hide backlink
```
````

会列出所有template目录下，在 OB插件 标题下方的未完成任务。

**查询语法**

![](https://pic2.zhimg.com/80/v2-19651aa39459deba2c2d846fa2c301a5_720w.webp)

![](https://pic2.zhimg.com/80/v2-daaa3afb56c05fdd0ad53dba3d2390ed_720w.webp)

## 全局过滤设置

![](https://pic3.zhimg.com/80/v2-7b6cb15121b69ca65b9ccd397061032a_720w.webp)

在插件设置页可以设置一个全局过滤关键字，可以是标签，也可以是其他文字，这样一来，查询的内容就会只查询包含该关键字的任务。  

## 示例

````text
```tasks
not done
due today
sort by due
```

```tasks
done
sort by priority reverse
```

```tasks
not done
due before next monday
sort by status
sort by description reverse
sort by path
```

```tasks
no due date
path includes GitHub
hide recurrence rule
hide task count
hide backlink
```

```tasks
not done
heading includes OB插件
hide backlink
exclude sub-items
short mode
limit 2
sort by priority
```
````

以上就是本篇分享的主要内容，建议在添加任务的时候，可以选择通过命令面板添加，会弹出交互窗口，在查询过程中，可根据个人需要添加对应的属性

文章内容是个人敲字截图总结的一些心得和使用体验，创作不易，欢迎 点赞、投币、收藏~ 作者：艾了哈 

**官方说明文档：**

[https://schemar.github.io/obsidian-tasks/getting-started/](https://link.zhihu.com/?target=https%3A//schemar.github.io/obsidian-tasks/getting-started/)