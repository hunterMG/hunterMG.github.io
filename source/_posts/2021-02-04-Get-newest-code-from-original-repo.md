---
title: Get newest code from original repo
date: 2021-02-04 14:53:50
categories:
tags:
---

Clone 开源项目后，在本地进行了修改，并提交到了自己的远程repo，如何从原 git 仓库获取最新的代码并 push 到自己的 repo。
<!-- more -->
## 场景
从 Github 上看到了一个开源项目，想自己部署，但是由于项目代码中的配置文件需要某软件的账号和 token，而原仓库中由于是开源项目所以是一个公用的账号，而自己部署的话肯定还是得用自己的，这里我选择使用私有仓库，一定程度上防止 token 泄漏。

因为 Fork 后的仓库不能设为私有，我只能先将代码拉取到本地：
```shell
git clone https://github.com/Stitchpunk/atari-ai.git test
```
在 Github 上建一个私有仓库，如：`test`.

在本地仓库中将远程仓库 `origin` 改为自己私有仓库的地址：

```shell
$ cd test
$ git remote -v # check remote repo address
origin  git@github.com:hunterMG/test.git (fetch)
origin  git@github.com:hunterMG/test.git (push)
$ git remote remove origin # remove remote repo
$ git remote add origin git@github.com:hunterMG/test.git # add remote repo of yourself
$ git push -u origin master # push code to your remote repo
```
这时候就可以在本地写代码，提交到本地仓库，再 push 到 Github 之后结合一些 CD/CI 的方法部署了。

## 原 repo 更新代码后如何更新自己的repo
几天之后新的问题来了：原开源仓库更新了新的 feature ，我想加到自己的仓库里，因为不是直接用 Github 的 Web 端 Fork 的仓库，所以不能用这篇[Github中进行fork后，原仓库更新了如何与原仓库同步](https://www.cnblogs.com/gqzdev/p/11900362.html)里提到的方法（直接在 Web 端发起 PR 从原仓库更新）。

然后看到了这个：[GIT - 拉取其他仓库的某个分支的代码](https://juejin.cn/post/6844903761593237511), 感觉差不多，试试还真行。

1. 添加远程 repo，
```shell
git remote add Stitchpunk https://github.com/Stitchpunk/atari-ai.git
```
2. 拉取远程 repo 信息：
```shell
git fetch Stitchpunk
```
3. 新建并切换到分支：
```shell
git checkout -b mergeStitchpunk #or git switch -c mergeStitchpunk
```
4. 合并最新的代码，解决冲突：
```shell
git merge Stitchpunk/master
```

### 更新自己 repo 主分支的代码

5. 切换到 master 分支
```shell
git checkout master #or git switch master
```

6. 合并分支
```shell
git merge mergeStitchpunk
```

7. 删除临时分支（optional）
```shell
git branch -d mergeStitchpunk
```

不过现在看来好像可以在第 4 步时直接切换到 master 分支，将代码直接合并到 master 上。😂

## Reference

[Github中进行fork后，原仓库更新了如何与原仓库同步](https://www.cnblogs.com/gqzdev/p/11900362.html)

[GIT - 拉取其他仓库的某个分支的代码](https://juejin.cn/post/6844903761593237511)

[创建与合并分支](https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424)