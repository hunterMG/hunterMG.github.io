---
title: learn python
date: 2018-03-26 09:57:47
categories: note
tags:
- python
- numpy
---

Note for python functions.
<!-- more -->
## [Counter()](http://www.pythoner.com/205.html)

## [most_common()](http://www.pythoner.com/205.html)

# Numpy
## [sum](https://docs.scipy.org/doc/numpy-1.14.0/reference/generated/numpy.sum.html#numpy.sum)
Calculate the summary of ndarray a according to the given axis, axis is a integer or tuple

## [argsort]()
返回数组的排序索引（从小到大），若为矩阵可指定轴。
```python
k = 2
x = np.array([[4, 5, 1],
              [1, 2, 3]])
top_k_idx = np.argsort(x[0])[:k]
print(x)
print(top_k_idx)

top_k = np.argsort(x, axis=1)
print(top_k)
```

## np.square
`np.square`是用c实现的，比`**`快多了😭。 661s:36s.

## np.linalg.norm
求[范数](https://blog.csdn.net/shijing_0214/article/details/51757564)，L1范数->曼哈顿距离（L1距离），L2范数->欧氏距离（L2距离）[np.linalg.norm](https://blog.csdn.net/hqh131360239/article/details/79061535)

## [vstack, hstack](https://docs.scipy.org/doc/numpy/reference/generated/numpy.vstack.html)
[refer](https://blog.csdn.net/csdn15698845876/article/details/73380803)

## [array_split](https://docs.scipy.org/doc/numpy/reference/generated/numpy.array_split.html)

## [concatenate](https://blog.csdn.net/garfielder007/article/details/51378296)

## np.random.choice
生成随机序列，可指定范围或来源于某个数组。
## reshape
改变矩阵为指定形状
## mean
求平均值，可指定matrix的轴
## fmax(x1, x2)
Compare two arrays and returns a new array containing the element-wise maxima. x2可以是一个数。

## numpy.random.randn(d0, d1, ..., dn)
从标准正态分布中返回一个或多个样本值。
## numpy.random.rand(d0, d1, …, dn)
随机样本位于[0, 1)中。

## \xa0 \n
```python
>>> s
'T-shirt\xa0\xa0短袖圆领衫,体恤衫\xa0'
>>> out = "".join(s.split())
>>> out
'T-shirt短袖圆领衫,体恤衫'
```

## [list comprehension](https://www.pythonforbeginners.com/basics/list-comprehensions-in-python)
```python
new_list = [ expression(i) for i in old_list if filter(i)]
>>> data[0]
{'votes': {'funny': 0, 'useful': 5, 'cool': 2},
 'user_id': 'rLtl8ZkDX5vH5nAx9C3q5Q',
 'review_id': 'fWKvX83p0-ka4JS3dc6E5A'}
>>> votes = pd.DataFrame([i['votes'] for i in data]) # data is a list which element is dict.
```
## np.random.shuffle()
```python
In [26]: x = np.arange(10)

In [27]: x
Out[27]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

In [28]: np.random.shuffle(x)

In [29]: x
Out[29]: array([8, 3, 4, 7, 9, 5, 1, 6, 0, 2])
```

## pandas Dataframe get rows by index array
```python
def split_data(data):
    x_num = data.shape[0]
    x_idx = np.arange(x_num)
    np.random.shuffle(x_idx)
    x_idx_train = x_idx[0 : int(x_num*0.7)]
    x_idx_test = x_idx[int(x_num*0.7) : ]
    train = data.iloc[x_idx_train]
    test = data.iloc[x_idx_test]
    return train, test
```

## str clean
```python
str1 = ''.join(str1.split()) # remove '\n','\xa0'
```

## pandas : add a column to a Daraframe
```python
data_df['cool'] = votes['cool']
```

## pandas : groupby mean
```python
data_df.groupby('stars').mean()
```