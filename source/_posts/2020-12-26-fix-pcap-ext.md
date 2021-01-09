---
title: 使用shell脚本批量检查pcap文件头并修改文件扩展名
date: 2020-12-26 15:10:13
categories: Shell
tags:
- shell
- Linux
---

最近在使用一个数据集时遇到了一个问题，正常 .pcap 文件的文件头是 `0xa1b2c3d4`, 但该数据集中的部分 .pcap 文件的文件头却是 .pcapng 文件的 `0x0a0d0d0a`, 将文件的扩展名更改即可，否则用 dpkt 包处理时会出错。（具体原因见[dpkt invalid tcpdump header error](https://stackoverflow.com/questions/23523524/dpkt-invalid-tcpdump-header-error)）,所以实现一个脚本来检查 .pcap 文件的文件头并修改错误文件的扩展名为 `.pcapng`.
<!-- more -->

批量检查pcap文件头并改正的脚本: `fixPcapExt.sh`  
参数：放置pcap文件的目录  
思路：
```shell
遍历目录中的文件：  
    如果文件的扩展名是否是`.pcap`：  
        如果文件的前4个字节为`0x0a0d0d0a`:  
            修改文件名  
```
实现：
```shell
#!/bin/bash
# fix wrong pcap file extention.
# Arg:  dir path where pcap files at.
dir=$1
cd $dir
for file in `ls .`
    do
    if [ "${file##*.}"x = "pcap"x ];then
        if [ `hexdump -n 4 -e '4 "%x"' $file`x = "a0d0d0a"x ];then
            echo $file
            name=$(ls $file | cut -d. -f1) 
            mv $file "$name".pcapng
        fi
    fi
    done
```
要点：
1. if [ "${file##*.}"x = "pcap"x ];then   
   
   ${file##*.} 是提取文件扩展名，##是贪婪操作符，从左至右匹配，匹配到最右边的.号，移除包含.号的左边内容；  
   x 是防止字符串为空时报错；  
   这一行中的空格都不能去掉，会报错。

2. \`hexdump -n 4 -e '4 "%x"' $file\`

    用hexdump检查前4个字节，并格式化输出

3. name=$(ls $file | cut -d. -f1) 

    提取文件名中的名字部分。  
    `cut -d. -f1` :提取以点为分隔符的第1部分。  
