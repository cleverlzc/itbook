# 查找表

原文地址：[https://baike.baidu.com/item/%E6%9F%A5%E6%89%BE%E8%A1%A8](https://baike.baidu.com/item/%E6%9F%A5%E6%89%BE%E8%A1%A8)


在计算机科学中，查找表是用简单的查询操作替换运行时计算的数组或者 associative array 这样的数据结构。

一个经典的例子就是三角表。每次计算所需的正弦值在一些应用中可能会慢得无法忍受，为了避免这种情况，应用程序可以在刚开始的一段时间计算一定数量的角度的正弦值，譬如计算每个整数角度的正弦值，在后面的程序需要正弦值的时候，使用查找表从内存中提取临近角度的正弦值而不是使用数学公式进行计算。


何时构建查找表有两个基本的约束条件，一个是可用内存的数量；不能构建一个超过能用内存空间的表格，尽管可以构建一个以查找速度为代价的基于磁盘的查找表。另外一个约束条件是初始计算查找表的时间——尽管这项工作不需要经常做，但是如果耗费的时间不可接受，那么也不适合使用查找表。