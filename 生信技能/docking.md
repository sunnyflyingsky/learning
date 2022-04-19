## 实用的工具和方法



###### chimera

delete

```
delete atom-spec
delete selected
```

select

~~~
select atom-spec    #选择指定目标的

select ~atom-spec   #选择指定目标之外的

select [ up | down | invert | invert sel ]
 Keyword arguments:
    up - broaden the selection
    down - narrow the selection
    invert - invert the selection within all models, like Select... Invert (all models) in the menu
    invert sel - invert the selection within models containing selections only, like Select... Invert (selected models) in the menu 
~~~

addh

~~~
addh options
~~~

addcharge

~~~
 Add Charge
 addcharge
~~~

open & write

~~~
open PDB ID
open filepath

close all

write format pdb #0 filepath
~~~



python

~~~
mols = chimera.openModels.list(modelTypes=[chimera.Molecule])
prep(receptor, method='gas', addHFunc=AddH.simpleAddHydrogens)
rec_charge = estimateFormalCharge(receptor[0].atoms)
~~~



###### vina



~~~shell
vina --receptor receptor.pdbqt --ligand ligand.pdbqt --center_x x --center_y y --center_z z --size_x dx --size_y dy --size_z dz --out out.pdbqt
#指定窗口大小为dx,dy,dz的一个立方体搜索空间，搜索中心位点为(x,y,z),然后针对receptor的这个位置检索ligand的docking状态
~~~



###### dock6



~~~
step1

step2

step3

step4
~~~







###### preparation

* 去除receptor和ligand之外其他分子或原子，

  比如金属离子，

* 分离receptor和ligand

* 加氢

* 加电荷和极性

* 去除水分子，如solvent 





###### 计算药物分子设计

1. 结构-功能-计算

* 靶点结构 -> 药物调控靶点的功能，药物与靶点的interaction

* 计算理论：热力学平衡 (熵, 焓)
  自由能 δG = -RTlnK = δH-TδS
  K = e^(-δG/(RT)) 活性，亲和力
  K1/K2 = e^(-δδG/(RT)) 选择性，靶向性
  10 fold potency difference -> 能量相差1.36kcal/mol (100 -> 2.72)
  静电作用，电子密度(电子密度低的原子，比如H，容易去结合电子密度高的原子，比如O)，在公式中表现为δH
  亲水疏水作用,极性，在公式中表现为δS
  氢键,距离决定能量(classical: O-H……O- & non-classical: C-H……O-)
  范德华方程,压强体积公式的修正模式,库仑力
  π-π stacking, 盐桥, π-盐离子 stacking, Amide(-π) stacking
  排斥力，吸引力（距离决定），精确的能量计算
  分子张力，转动力，自旋电场

* 溶液条件下的蛋白、RNA一般比较flexible，小分子和它们结合后，一般会将柔性结构转化为刚性结构，导致蛋白或者RNA部分位点flexibility降低，溶解性降低，能量也会发生改变

2. 代表性药物

   * 肽环结构，

   * 基于底物相似的药物设计，活性位点

   * 芳香环，疏水，一般利于进入pocket(pocket一般深埋，疏水)，
     芳香环内

   * 成环和开环问题
     将一些链状成环，来固定结构，使得键角面角不能够再转动，
     减小环的原子数，使环尽可能向五元六元环靠拢，易于合成
     开环能够增大水溶性，易于合成，代谢稳定性
     关环可以增大疏水性，保证一些疏水亲和

   * 设计过程中区分活性基团和非活性部分，尽可能修饰非活性部分

3. 分析方法
     * 计算不同键的距离大小,能量大小问题
     * 通过实验结果,进行统计计算,然后去除原子半径效应,
     * 一般计算不加入范德华力,只用来优化小分子,不用来计算活性,因为这种范德华力更多体现为噪音,计算不能精确而影响又太大
       针对小分子的结构进行类似于穷举的转动、变构，然后针对这些结构计算能量排序，找出能量最低的几种构型
     * 计算量化指标，深度学习量化化学指标 ChemAxon
     * Scoring function
       Parameters for H-bond(<3A), H-bond(3~3.5A), Non-polar surface area, 然后做深度学习        当然，这里只是举一个例子，其中存在的缺陷还很多，比如动态性等
     * 分子动力学方式检索，获取多种构想的complex  macromolecule
     * 多构想之间可以进行能量计算
     * 检索势能面，一般能量在0.5倍最高能量(能量分布方程的中位数)以下是比较合适的。估算能量指标尺度。
     * 在排斥力的两个高电子密度高的两个原子中间加入某个取代基，使得其中包含一个低电子密度的原子，
     * 2nd step的计算药物分子设计
     * 逐步计算修改设计的方式
       每进行一次计算，根据计算结果进行经验计算，然后修饰/修改，然后迭代计算
       添加，删除的一种迭代方法
     * Ligand based de novo Design
       基于小分子药物的模式，计算活性亲和的空腔，然后再用大分子去匹配这个空腔的模式
     * 基于活性中心的de novo设计
4. challenge
   * selectivity、toxicity
   * 能量计算误差很大
   * RNA 的flexibility太多，全部考虑可能很影响计算
   * how to avoid false positive
   * 小分子的diversity
   * 软件和文献中存在一些wrong examples，对某一些静电力考虑不够全面，H键的计算以及加氢的手段不一
   * 传统基于经验的方式和AI方式的一个很大的差别是，能够获取更多diversity的结构，而AI得方式的模式变化不大，不能跳出一些桎梏，这是一个值得思考的地方