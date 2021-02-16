---
title: 批量重命名文件
date: 2021-02-07 01:06:11
categories: tool
tags:
- Linux
- Shell
---

用 Shell 脚本重命名文件夹中所有的文件，根据文件的修改时间排序，将文件名改为从 `001` 开始的数字。根据[使用shell脚本批量检查pcap文件头并修改文件扩展名](/2020/12/26/fix-pcap-ext/index.html)中写的脚本修改而来。
<!-- more -->

rename.sh :
```shell
#!/bin/bash
# Raname file to numbers by file modification time.
# Arg:  dir path where files at.
dir=$1
cd $dir
file_num=$(ls -l | wc -l);
echo $file_num
cnt=1
for file in *;
do
    echo $file
    ext=$(ls "$file" | cut -d. -f2)
    mv "$file" $(printf "%03d" $cnt).$ext
    cnt=`expr $cnt + 1`
done
```
用法：
```shell
rename.sh dir1
```
dir1 是需要批量重命名的文件所在的文件夹路径。

## 2021/02/16 fix：不能处理含空格的文件名。
```diff
-for file in `ls -rt`;
+for file in *;
```
\`ls -rt\` 会被文件名的空格分割， * 则不会。

```diff
 do
     echo $file
-    name=$(ls $file | cut -d. -f1)
```
移除无用代码。

```diff
-    ext=$(ls $file | cut -d. -f2)
-    mv $file $(printf "%03d" $cnt).$ext
+    ext=$(ls "$file" | cut -d. -f2)
+    mv "$file" $(printf "%03d" $cnt).$ext
```
被 `""` 包裹的文件名中即使含空格也不需要转义。