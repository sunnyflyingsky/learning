## nucleosome occupancy

核小体占用  

* 定义



* 如何获取，pipeline

其描述的是同一位点存在核小体在部分细胞中占全部细胞中的比例，我们通过计算核小体的start to end位点之间的累计值形成整个基因组上位点的核小体累积值，进而形成全基因组的每个位点的核小体占有率

* 输出形式

位点与位点对应峰值的形式

```
position  score
0 0
1 0
2 12
3 45
4 129
5 120
6 87
……
```

* 后续分析流程

可视化输出，波浪线

绘制特定位点的核小体占有情况，如TSS区域

![image-20201126144924282](C:\Users\云在碧端\AppData\Roaming\Typora\typora-user-images\image-20201126144924282.png)







***********


## nucleoATAC

文献中能够利用ATAC-seq数据检测nucleosome occupancy的工具。



#### 安装





#### 使用


















