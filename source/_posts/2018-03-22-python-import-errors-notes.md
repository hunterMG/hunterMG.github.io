---
title: python errors notes
date: 2018-03-22 16:57:37
categories: development
tags:
- python
---

environment: python3.6, not tested on python2.x
<!--more-->
## ImportError: cannot import name imread
```sh
pip install Pillow==2.6.0
```

## ModuleNotFoundError: No module named 'past'
```sh
pip install future
```

## ValueError: unknown locale: UTF-8
```sh
vi ~/.zshrc
```
>   export LC_ALL=en_US.UTF-8  
    export LANG=en_US.UTF-8
```sh
source ~/.zshrc
```