---
title: macOS Markdown 预览和 lsd 进程占用 CPU
date: 2021-01-28 11:11:07
categories: macOS
tags:
- macOS
- shell
- qlmarkdown
- qlcolorcode
- preview
---

Mac 上一直用“一指禅”预览文件，但是它本身不支持 Markdown 文件的预览，所以用 brew 装了 [qlmarkdown](https://github.com/toland/qlmarkdown) 这个插件。但不知从什么时候起，可能是某次 OS 版本更新之后吧，就不能预览 Markdown 文件了。
<!-- more -->
## 初现端倪
在 github 的 issue 里找到了这个 [issue 39](https://github.com/toland/qlmarkdown/issues/39), 说只能预览 `.markdown` 扩展名的文件，于是我把 `.md` 文件的扩展名改成了 `.markdown`，果真可以预览了，看来问题是一样的。

于是我就往下翻评论，看到了有个老哥给出了下面的这个命令，重建 Launch Service 数据库，有人说有用，我就尝试了一下。结果马上就能预览 `.md` 扩展名的文件了。

```shell
/System/Library/Frameworks/CoreServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -kill -r -domain local -domain system -domain user ; killall Dock
```
## 节外生枝
但又一个问题出现了：`lsd` 进程一直在占用 30-40%的cpu，有时候能到 100%，过了一天（22小时）之后还是如此，用`Monitor`看了一下，磁盘写入量达到了 46G，这也太不正常了，于是又找解决方法。

然后看到了这个[/usr/libexec/lsd 占用 100% CPU 的解决办法](https://www.logcg.com/archives/3382.html#comment-109590)，发现有两个命令，一个和上面的相同，另一个如下：

```shell
find /System/Library/Frameworks -type f -name "lsregister" -exec {} -kill -seed -r \;
```
这篇文章说这两个命令是一样的，其实参数不一样。在 `lsd` 持续占用 CPU时，我运行了下面这个命令，效果立竿见影，`lsd`马上就不占用 CPU了。就在我以为万事大吉，准备收工，这篇记录写了一半时，我突然发现 Markdown 的预览又不起作用了，，，重启无效，，，
## 峰回路转
正当我欲哭无泪时，我想到了 Finder 的一些插件经常会冲突，比如坚果云和 OneDrive 的文件同步状态显示插件就经常失效，需要在系统设置里的插件设置中重启一下才行。我就想会不会是和别的预览插件冲突了。除了 qlmarkdown 之外，我还装了 qlcolorcode 用于在预览源代码文件时提供语法高亮等。然后列出了一下 `~/Library/QuickLook` 目录，准备一个一个排除。
```shell
lrwxr-xr-x   1 yg  staff    73B 11 12  2019 NSQuickLookPlugin.qlgenerator -> /Applications/Nutstore.app/Contents/PlugIns/NSQuickLookPlugin.qlgenerator
drwxr-xr-x@  3 yg  staff    96B 12 16 03:36 QLColorCode.qlgenerator
drwxr-xr-x   3 yg  staff    96B 10 22  2019 QLMarkdown.qlgenerator
drwxr-xr-x   3 yg  staff    96B  2  4  2020 QLStephen.qlgenerator
drwxr-xr-x@  3 yg  staff    96B  7 18  2016 QuickLookASE.qlgenerator
drwxr-xr-x@  3 yg  staff    96B  2  3  2013 QuickLookCSV.qlgenerator
drwxr-xr-x@  3 yg  staff    96B 10 22  2019 QuickLookJSON.qlgenerator
drwxr-xr-x@  3 yg  staff    96B  2 28  2018 WebpQuickLook.qlgenerator
drwxr-xr-x@  3 yg  staff    96B  3 24  2019 qlImageSize.qlgenerator
```

第一个就是坚果云的，其实我首先想到的是quicklook插件似乎是在 `Info.plist` 里声明了 preview 的文件类型，在这里还有一个冷知识：
> Launch Service 是用于将文件类型和打开该类型文件的程序关联起来的。  
macOS 中的文件类型并不是靠文件的扩展名区分的，而是文件的 `kMDItemContentType`,可以用以下命令查看一个文件的类型：

```shell
$ mdls README.md
_kMDItemDisplayNameWithExtensions      = "README.md"
kMDItemContentCreationDate             = 2021-01-26 18:22:18 +0000
kMDItemContentCreationDate_Ranking     = 2021-01-26 00:00:00 +0000
kMDItemContentModificationDate         = 2021-01-26 18:22:18 +0000
kMDItemContentModificationDate_Ranking = 2021-01-26 00:00:00 +0000
kMDItemContentType                     = "net.daringfireball.markdown"
kMDItemContentTypeTree                 = (
    "net.daringfireball.markdown",
    "public.plain-text",
    "public.text",
    "public.data",
    "public.item",
    "public.content"
)
kMDItemDateAdded                       = 2021-01-26 18:22:18 +0000
kMDItemDateAdded_Ranking               = 2021-01-26 00:00:00 +0000
kMDItemDisplayName                     = "README.md"
kMDItemDocumentIdentifier              = 0
kMDItemFSContentChangeDate             = 2021-01-26 18:22:18 +0000
kMDItemFSCreationDate                  = 2021-01-26 18:22:18 +0000
kMDItemFSCreatorCode                   = ""
kMDItemFSFinderFlags                   = 0
kMDItemFSHasCustomIcon                 = (null)
kMDItemFSInvisible                     = 0
kMDItemFSIsExtensionHidden             = 0
kMDItemFSIsStationery                  = (null)
kMDItemFSLabel                         = 0
kMDItemFSName                          = "README.md"
kMDItemFSNodeCount                     = (null)
kMDItemFSOwnerGroupID                  = 20
kMDItemFSOwnerUserID                   = 501
kMDItemFSSize                          = 2550
kMDItemFSTypeCode                      = ""
kMDItemInterestingDate_Ranking         = 2021-01-27 00:00:00 +0000
kMDItemKind                            = "Markdown"
kMDItemLastUsedDate                    = 2021-01-27 03:35:22 +0000
kMDItemLastUsedDate_Ranking            = 2021-01-27 00:00:00 +0000
kMDItemLogicalSize                     = 2550
kMDItemPhysicalSize                    = 4096
kMDItemUseCount                        = 2
kMDItemUsedDates                       = (
    "2021-01-26 16:00:00 +0000"
)
```
所以我先检查了 `NSQuickLookPlugin.qlgenerator` 的 `Info.plist` 中的 `LSItemContentTypes` 的值，但并没找到任何与 `qlmarkdown` 冲突的部分。所以还是直接卸载坚果云试试，反正现在也不用坚果云了。
## 万事大吉
然后， qlmarkdown works fine❕❕❕😭

此时此刻，我只想说：Stupid software。

或许这篇记录现在可以改名为 `坚果云引发的惨案`。

# Reference
[qlmarkdown issue 39](https://github.com/toland/qlmarkdown/issues/39)  
[/usr/libexec/lsd 占用 100% CPU 的解决办法](https://www.logcg.com/archives/3382.html#comment-109590)  