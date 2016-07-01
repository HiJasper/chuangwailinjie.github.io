title: 大量数据排序问题-ASCII
date: 2016-04-27 17:17:22
tags: algorithm
toc: true
categories: algorithm
---


# 问题引出 #

这个问题最开始是在一个朋友遇到的需求:

>已知：300万个随机的char（即取值范围为0-127），有大量的重复。求：最快的排序方法，将这300万个char，按照从大到小的顺序排列。内存最好在9M之内.

刚开始拿到这个问题的时候，由于我本身只对几个简单的常规排序有印象，搜索下来，发现这个问题使用这些方法都不太合适。内存限制在9M，也就是9369216B的内存。300w个char类型数据在C家族中占用的内存大小是3000000B。

# 分析 #

有几位朋友提出使用Bucket sort、或者链表之类的数据结构。<!--more-->链表的情况下，需要付出额外的内部空间保持指针指向，对于内存的限制就不能很好的体现。这里我的想法是一次性读入300w的数据，所以下面的展开也都是在这个基础上。

桶排序是个很不错的方法。

>工作的原理是将数组分到有限数量的桶子里。每个桶子再个别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排序）。桶排序是鸽巢排序的一种归纳结果。当要被排序的数组内的数值是均匀分配的时候，桶排序使用线性时间（Θ（n））。但桶排序并不是 比较排序，他不受到 O(n log n) 下限的影响。

这边先来看一个类似的例子。

## 高考海量数据排序 ##

>一年的全国高考考生人数为500 万，分数使用标准分，最低100 ，最高900 ，没有小数，要求对这500 万元素的数组进行排序。

对500W数据排序，如果基于比较的先进排序，平均比较次数为O(5000000*log5000000)≈1.112亿。但是我们发现，这些数据都有特殊的条件： 100=<score<=900。那么我们就可以考虑桶排序这样一个“投机取巧”的办法、让其在毫秒级别就完成500万排序。

方法：创建801(900-100)个桶。将每个考生的分数丢进f(score)=score-100的桶中。这个过程从头到尾遍历一遍数据只需要500W次。然后根据桶号大小依次将桶中数值输出，即可以得到一个有序的序列。而且可以很容易的得到100分有xxx人，501分有xxx人。
实际上，桶排序对数据的条件有特殊要求，如果上面的分数不是从100-900，而是从0-2亿，那么分配2亿个桶显然是不可能的。所以桶排序有其局限性，适合元素值集合并不大的情况。

这个问题让我想到了《编程珠玑》的引言部分，著者因为内存的限制，通过使用位图BitMap为整型数据排序，实际上跟这两个问题都是异曲同工。


# 解决 #

我们申请128个变量（对应ASCII的128个数据），遍历300w 的数据，并对他们计数，保存在变量中。最后这128个变量中保留的就是每个ASCII码对应出现的次数。这个类似于Bucket Sort中的木桶。只不过桶排序有一个限制条件，所有数据必须是相同的整数。

然后遍历这128个变量，例如`a[22]`中保留的是ASCII中第23个数据出现的次数。则在相应位置设置`a[22]`个22.

```C++
#include<iostream>
#include<stdlib.h>
#include<string.h>
#define Random(x) (rand() % x)
using namespace std;

int _tmain(int argc, _TCHAR* argv[])
{
	char value[1000000];
	int countx[128];
	memset(countx, 0, sizeof(countx));
	for (int i = 0; i < 1000000; i++){
		value[i] = Random(128);
		countx[value[i]]++;
	}
	int court = 0;
	for (int i = 0; i < 128; i++){
		for (int x = 0; x < countx[i]; x++)
			value[court++] = i;
	}
	cout << "success" << '\n';
	return 0;
}
```
