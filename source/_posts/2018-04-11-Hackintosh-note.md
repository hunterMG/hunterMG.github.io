---
title: Hackintosh note
date: 2018-04-11 09:17:24
categories: note
tags:
- Hackintosh
- macOS
---

## Alfred
破解后每次开机都提示访问通讯录
```sh
sudo codesign -f -d -s - /Applications/Alfred\ 3.app/Contents/Frameworks/Alfred\ Framework.framework/Versions/A/Alfred\ Framework
```

## Video player
自带的QuickTime Player不能切换音轨  
[IINA](https://github.com/lhc70000/iina)，
[VLC](https://www.videolan.org/vlc/index.html)

## 根据当前输入法让菜单栏显示不同颜色
```shell
brew cask install showyedge
```
## [jupyter themes](https://github.com/dunovank/jupyter-themes)
```shell
pip install jupyterthemes
jt -l
jt -t onedork -T -f sourcemed
```