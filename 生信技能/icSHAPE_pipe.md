## icSHAPE-pipe tutorial

李盼 2021-05-20



> 所有的软件基本上都服务器上安装了，不需要再安装

[1. 加载路径](#1.加载路径)

[2. 定义文件路径](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#2-定义文件路径)

[3. 使用icSHAPE-pipe计算icSHAPE score](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#3-使用icshape-pipe计算icshape-score)

​    [3.1 获取icSHAPE-pipe功能模块](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#31-获取icshape-pipe功能模块)

​    [3.2 创建相关目录](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#32-创建相关目录)

​    [3.3 构建基因组索引](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#33-构建基因组索引)

​    [3.4 测序数据预处理](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#34-测序数据预处理)

​		[3.4.1 去重相同序列的reads，避免PCR duplicates](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#341-去重相同序列的reads避免pcr-duplicates)

​		[3.4.2 Trim 3'adaptor与5' random barcord](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#342-trim-3adaptor与5-random-barcord)

​		[3.4.3 去除map到rRNA上的reads](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#343-去除map到rrna上的reads)

​		[3.4.4 去除map到smallRNA上的reads](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#344-去除map到smallrna上的reads)

​	[3.5 Mapping](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#35-mapping)

​		[3.5.1 Map到基因组上](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#351-map到基因组上)

​		[3.5.2 计算RPKM](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#352-计算rpkm)

​	[3.6 计算score](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#36-计算score)

​		[3.6.1 文件格式转换](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#361-文件格式转换)

​		[3.6.2 计算icSHAPE score](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#362-计算icshape-score)

​		[3.6.3 基因组icSHAPE score转成转录本icSHAPE score](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#363-基因组icshape-score转成转录本icshape-score)

[4 可视化](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#4-可视化)

​	[4.1 基因组icSHAPE score转成转录本bedGraph](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#41-基因组icshape-score转成转录本bedgraph)

​	[4.2 预测一个RNA的二级结构，并可视化](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#42-预测一个rna的二级结构并可视化)

[5. 质量统计](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#5-质量统计)

​	[5.1 统计每一步的reads数量](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#51-统计每一步的reads数量)

​	[5.2 统计sam文件中的reads分布, D2,N1,N2都类似操作](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#52-统计sam文件中的reads分布-d2n1n2都类似操作)

​	[5.3 基因组上的icSHAPE值的分布](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#53-基因组上的icshape值的分布)

​	[5.4 转录本上的icSHAPE值的分布](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#54-转录本上的icshape值的分布)

​	[5.5 icSHAPE值与已知的二级结构之间的一致程度](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#55-icshape值与已知的二级结构之间的一致程度)

​	[5.6 搜索参数](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#56-搜索参数)

[6. One-line command](http://olddriver.website/Applications/icSHAPE-pipe-tutorial.html#6-one-line-command)

### 1. 加载路径

```
# 加载Python 3
source /Share/home/zhangqf/lipan/Load_Env/load_anaconda3.sh
# 加载icSHAPE-pipe
export PATH=/Share/home/zhangqf/lipan/usr/icSHAPE-pipe/bin:$PATH
```

### 2. 定义文件路径

```
IN=/Share2/home/zhangqf/lipan/usr/icSHAPE-pipe/example_data
D_1=$IN/raw_data/D1.chr22.fastq.gz   ## DMSO rep1
D_2=$IN/raw_data/D2.chr22.fastq.gz   ## DMSO rep2
N_1=$IN/raw_data/N1.chr22.fastq.gz   ## NAI-N3 rep1
N_2=$IN/raw_data/N2.chr22.fastq.gz   ## NAI-N3 rep2

HUMAN_ABUNDANT_RNA=$IN/rRNA/human_rRNA_tRNA_mtRNA.fa  ## Abundant RNAs
GTF=$IN/GTF/chr22.gtf   ## GTF注释文件
GENOME=$IN/fasta/chr22.fa  ## chr22 序列
ADAPTOR=$IN/adaptor/TruSeq2-PE.fa  ## adaptor
STR_18S=$IN/rRNA/human/human_small.dot
STR_28S=$IN/rRNA/human/human_large.dot

### 设置你自己的输出目录
OUT=/Share2/home/zhangqf7/tmp/hh  ## 输出目录
```

### 3. 使用icSHAPE-pipe计算icSHAPE score

> icSHAPE-pipe: http://zhanglab.net/resources/icSHAPE-pipe/home.html

#### 3.1 获取icSHAPE-pipe功能模块

```
icSHAPE-pipe
## 更多的帮助：http://zhanglab.net/resources/icSHAPE-pipe/manual.html
## 所有的命令都可以直接敲入，自动输出帮助信息，比如：
icSHAPE-pipe starbuild
```

#### 3.2 创建相关目录

```
mkdir -p ${OUT}/index
mkdir -p ${OUT}/rRNA_index
mkdir -p ${OUT}/smallRNA
mkdir -p ${OUT}/GTF
mkdir -p ${OUT}/1.readCollapse
mkdir -p ${OUT}/2.trim
mkdir -p ${OUT}/3.rem_rRNA
mkdir -p ${OUT}/4.mapSmallRNA
mkdir -p ${OUT}/5.mapGenome
mkdir -p ${OUT}/6.calcFPKM
mkdir -p ${OUT}/7.sam2tab
mkdir -p ${OUT}/8.calcGenomeSHAPE
mkdir -p ${OUT}/9.transSHAPE
mkdir -p ${OUT}/10.bedGraph
mkdir -p ${OUT}/11.quality_control
```

#### 3.3 构建基因组索引

```
# 对abundant RNA建索引：用bowtie2
bowtie2-build --quiet ${HUMAN_ABUNDANT_RNA} ${OUT}/rRNA_index/rRNA
falen ${HUMAN_ABUNDANT_RNA} > ${OUT}/rRNA_index/rRNA.len

# 对基因组建索引：用STAR
icSHAPE-pipe starbuild -i ${GENOME} -o ${OUT}/index/ --gtf ${GTF} -p 20

# 解析GTF注释文件
icSHAPE-pipe parseGTF -g ${GTF} -o ${OUT}/GTF/Anno -s ensembl --genome ${GENOME}

# 获取小于200的转录本构建小RNA基因组索引：用bowtie2
fetchSmallRNA.py ${OUT}/GTF/Anno_transcriptome.fa ${OUT}/smallRNA/smallRNA.fa 200
bowtie2-build --quiet ${OUT}/smallRNA/smallRNA.fa ${OUT}/smallRNA/smallRNA
falen ${OUT}/smallRNA/smallRNA.fa > ${OUT}/smallRNA/smallRNA.len
```

#### 3.4 测序数据预处理

##### 3.4.1 去重相同序列的reads，避免PCR duplicates

```
### 1. 去重相同序列的reads，避免PCR duplicates
icSHAPE-pipe readcollapse -U ${D_1} -o ${OUT}/1.readCollapse/D1.chr22.fastq --simplify
icSHAPE-pipe readcollapse -U ${D_2} -o ${OUT}/1.readCollapse/D2.chr22.fastq --simplify
icSHAPE-pipe readcollapse -U ${N_1} -o ${OUT}/1.readCollapse/N1.chr22.fastq --simplify
icSHAPE-pipe readcollapse -U ${N_2} -o ${OUT}/1.readCollapse/N2.chr22.fastq --simplify
```

##### 3.4.2 Trim 3'adaptor与5' random barcord

> ```
> 调用trimmimatic
> ```

```
icSHAPE-pipe trim -i ${OUT}/1.readCollapse/D1.chr22.fastq -o ${OUT}/2.trim/D1.chr22.fastq -l 13 -a ${ADAPTOR} -p 20 -m 25
icSHAPE-pipe trim -i ${OUT}/1.readCollapse/D2.chr22.fastq -o ${OUT}/2.trim/D2.chr22.fastq -l 13 -a ${ADAPTOR} -p 20 -m 25
icSHAPE-pipe trim -i ${OUT}/1.readCollapse/N1.chr22.fastq -o ${OUT}/2.trim/N1.chr22.fastq -l 13 -a ${ADAPTOR} -p 20 -m 25
icSHAPE-pipe trim -i ${OUT}/1.readCollapse/N2.chr22.fastq -o ${OUT}/2.trim/N2.chr22.fastq -l 13 -a ${ADAPTOR} -p 20 -m 25
```

##### 3.4.3 去除map到rRNA上的reads

> ```
> 调用Bowtie2
> ```

```
icSHAPE-pipe cleanFq -i ${OUT}/2.trim/D1.chr22.fastq -o ${OUT}/3.rem_rRNA/D1.chr22.fastq -x ${OUT}/rRNA_index/rRNA -p 20 --mode Local --sam ${OUT}/3.rem_rRNA/D1.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/2.trim/D2.chr22.fastq -o ${OUT}/3.rem_rRNA/D2.chr22.fastq -x ${OUT}/rRNA_index/rRNA -p 20 --mode Local --sam ${OUT}/3.rem_rRNA/D2.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/2.trim/N1.chr22.fastq -o ${OUT}/3.rem_rRNA/N1.chr22.fastq -x ${OUT}/rRNA_index/rRNA -p 20 --mode Local --sam ${OUT}/3.rem_rRNA/N1.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/2.trim/N2.chr22.fastq -o ${OUT}/3.rem_rRNA/N2.chr22.fastq -x ${OUT}/rRNA_index/rRNA -p 20 --mode Local --sam ${OUT}/3.rem_rRNA/N2.chr22.sam
```

##### 3.4.4 去除map到smallRNA上的reads

> 调用Bowtie2

```
icSHAPE-pipe cleanFq -i ${OUT}/3.rem_rRNA/D1.chr22.fastq -o ${OUT}/4.mapSmallRNA/D1.chr22.fastq -x ${OUT}/smallRNA/smallRNA -p 20 --mode Local --sam ${OUT}/4.mapSmallRNA/D1.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/3.rem_rRNA/D2.chr22.fastq -o ${OUT}/4.mapSmallRNA/D2.chr22.fastq -x ${OUT}/smallRNA/smallRNA -p 20 --mode Local --sam ${OUT}/4.mapSmallRNA/D2.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/3.rem_rRNA/N1.chr22.fastq -o ${OUT}/4.mapSmallRNA/N1.chr22.fastq -x ${OUT}/smallRNA/smallRNA -p 20 --mode Local --sam ${OUT}/4.mapSmallRNA/N1.chr22.sam
icSHAPE-pipe cleanFq -i ${OUT}/3.rem_rRNA/N2.chr22.fastq -o ${OUT}/4.mapSmallRNA/N2.chr22.fastq -x ${OUT}/smallRNA/smallRNA -p 20 --mode Local --sam ${OUT}/4.mapSmallRNA/N2.chr22.sam
```

#### 3.5 Mapping

##### 3.5.1 Map到基因组上

> 调用STAR

```
icSHAPE-pipe mapGenome -i ${OUT}/4.mapSmallRNA/D1.chr22.fastq -o ${OUT}/5.mapGenome/D1.chr22 -x ${OUT}/index -p 20 --noMut5
icSHAPE-pipe mapGenome -i ${OUT}/4.mapSmallRNA/D2.chr22.fastq -o ${OUT}/5.mapGenome/D2.chr22 -x ${OUT}/index -p 20 --noMut5
icSHAPE-pipe mapGenome -i ${OUT}/4.mapSmallRNA/N1.chr22.fastq -o ${OUT}/5.mapGenome/N1.chr22 -x ${OUT}/index -p 20 --noMut5
icSHAPE-pipe mapGenome -i ${OUT}/4.mapSmallRNA/N2.chr22.fastq -o ${OUT}/5.mapGenome/N2.chr22 -x ${OUT}/index -p 20 --noMut5
```

##### 3.5.2 计算RPKM

> 调用cufflinks

```
icSHAPE-pipe calcFPKM \
    -i ${OUT}/5.mapGenome/D1.chr22.sorted.bam \
    -o ${OUT}/6.calcFPKM/D1.chr22 \
    -G ${GTF} \
    -p 20

icSHAPE-pipe calcFPKM \
    -i ${OUT}/5.mapGenome/D2.chr22.sorted.bam \
    -o ${OUT}/6.calcFPKM/D2.chr22 \
    -G ${GTF} \
    -p 20
```

#### 3.6 计算score

##### 3.6.1 文件格式转换

```
icSHAPE-pipe sam2tab -in ${OUT}/5.mapGenome/D1.chr22.sorted.bam -out ${OUT}/7.sam2tab/D1.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/5.mapGenome/D2.chr22.sorted.bam -out ${OUT}/7.sam2tab/D2.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/5.mapGenome/N1.chr22.sorted.bam -out ${OUT}/7.sam2tab/N1.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/5.mapGenome/N2.chr22.sorted.bam -out ${OUT}/7.sam2tab/N2.chr22.tab

# for rRNA, smallRNA也用类似的方法处理
icSHAPE-pipe sam2tab -in ${OUT}/3.rem_rRNA/D1.chr22.sam -out ${OUT}/7.sam2tab/rRNA_D1.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/3.rem_rRNA/D2.chr22.sam -out ${OUT}/7.sam2tab/rRNA_D2.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/3.rem_rRNA/N1.chr22.sam -out ${OUT}/7.sam2tab/rRNA_N1.chr22.tab
icSHAPE-pipe sam2tab -in ${OUT}/3.rem_rRNA/N2.chr22.sam -out ${OUT}/7.sam2tab/rRNA_N2.chr22.tab
```

##### 3.6.2 计算icSHAPE score

```
icSHAPE-pipe calcSHAPE \
    -D ${OUT}/7.sam2tab/D2.chr22.tab,${OUT}/7.sam2tab/D1.chr22.tab \
    -N ${OUT}/7.sam2tab/N2.chr22.tab,${OUT}/7.sam2tab/N1.chr22.tab \
    -size ${OUT}/index/chrNameLength.txt \
    -ijf ${OUT}/index/sjdbList.fromGTF.out.tab \
    -genome ${GENOME} \
    -bases A,T,C,G \ # 表示计算A,T,C,G四个碱基的结构分数
    -out ${OUT}/8.calcGenomeSHAPE/shape.gTab
```

对于rRNA、sRNA等单独序列的RNA，则不加`-ijf`参数：

```
icSHAPE-pipe calcSHAPE \
    -D ${OUT}/7.sam2tab/rRNA_D2.chr22.tab,${OUT}/7.sam2tab/rRNA_D1.chr22.tab \
    -N ${OUT}/7.sam2tab/rRNA_N2.chr22.tab,${OUT}/7.sam2tab/rRNA_N1.chr22.tab \
    -size ${OUT}/rRNA_index/rRNA.len \
    -genome ${HUMAN_ABUNDANT_RNA} \
    -bases A,T,C,G \
    -out ${OUT}/8.calcGenomeSHAPE/rRNA_shape.gTab \
    -non-sliding # -non-sliding表示不使用滑动窗口计算结构分数
```

如果是smartSHAPE文库，则不需要使用DMSO样本：

```
icSHAPE-pipe calcSHAPENoCont \
    -N ${OUT}/7.sam2tab/N2.chr22.tab,${OUT}/7.sam2tab/N1.chr22.tab \
    -size ${OUT}/index/chrNameLength.txt \
    -ijf ${OUT}/index/sjdbList.fromGTF.out.tab \
    -genome ${GENOME} \
    -bases A,T,C,G \
    -out ${OUT}/8.calcGenomeSHAPE/shape.gTab
```

##### 3.6.3 基因组icSHAPE score转成转录本icSHAPE score

```
icSHAPE-pipe genSHAPEToTransSHAPE \
    -i ${OUT}/8.calcGenomeSHAPE/shape.gTab \
    -o ${OUT}/9.transSHAPE/final.shape \
    -g ${OUT}/GTF/Anno.genomeCoor.bed \
    -p 20 \
    -r ${OUT}/6.calcFPKM/D2.chr22/isoforms.fpkm_tracking,${OUT}/6.calcFPKM/D1.chr22/isoforms.fpkm_tracking

# for rRNA, smallRNA也用类似的方法处理
icSHAPE-pipe genSHAPEToTransSHAPE \
    -i ${OUT}/8.calcGenomeSHAPE/rRNA_shape.gTab \
    -o ${OUT}/9.transSHAPE/final.shape \
    -s ${OUT}/rRNA_index/rRNA.len \
    -p 1 \
    --app
```

### 4 可视化

#### 4.1 基因组icSHAPE score转成转录本bedGraph

```
icSHAPE-pipe genSHAPEToBedGraph \
    -i ${OUT}/8.calcGenomeSHAPE/shape.gTab \
    -o ${OUT}/10.bedGraph/ \
    -c 200
```

下载安装igv: https://software.broadinstitute.org/software/igv/. 把基因组fasta文件和bedGraph文件导入IGV可视化。

#### 4.2 预测一个RNA的二级结构，并可视化

首先，需要下载一个VARNA: http://varna.lri.fr/index.php?lang=en&page=downloads&css=varna。下载到你自己的电脑上，并记住它的目录，比如：`/Users/lee/Library/VARNAv3-93.jar`。在服务器上打开交互式python，输入：

```
import General, Structure, Visual

fasta = General.load_fasta("${OUT}/GTF/Anno_transcriptome.fa")
shape = General.load_shape("${OUT}/9.transSHAPE/final.shape")
seq, shape = fasta['ENST00000467920.1'], shape['ENST00000467920.1']

dot = Structure.predict_structure(seq, shape)
cmd = Visual.Plot_RNAStructure_Shape(seq, dot, shape, VARNAProg=r'/Users/lee/Library/VARNAv3-93.jar')
print(cmd)   ### 粘贴这条命令到本地计算机终端
```

### 5. 质量统计

#### 5.1 统计每一步的reads数量

```
icSHAPE-pipe readDistributionStatistic \
    -@ ${D_1},${OUT}/1.readCollapse/D1.chr22.fastq,${OUT}/2.trim/D1.chr22.fastq,${OUT}/3.rem_rRNA/D1.chr22.fastq,${OUT}/4.mapSmallRNA/D1.chr22.fastq,${OUT}/5.mapGenome/D1.chr22.unsorted.bam \
    -@ ${D_2},${OUT}/1.readCollapse/D2.chr22.fastq,${OUT}/2.trim/D2.chr22.fastq,${OUT}/3.rem_rRNA/D2.chr22.fastq,${OUT}/4.mapSmallRNA/D2.chr22.fastq,${OUT}/5.mapGenome/D2.chr22.unsorted.bam \
    -@ ${N_1},${OUT}/1.readCollapse/N1.chr22.fastq,${OUT}/2.trim/N1.chr22.fastq,${OUT}/3.rem_rRNA/N1.chr22.fastq,${OUT}/4.mapSmallRNA/N1.chr22.fastq,${OUT}/5.mapGenome/N1.chr22.unsorted.bam \
    -@ ${N_2},${OUT}/1.readCollapse/N2.chr22.fastq,${OUT}/2.trim/N2.chr22.fastq,${OUT}/3.rem_rRNA/N2.chr22.fastq,${OUT}/4.mapSmallRNA/N2.chr22.fastq,${OUT}/5.mapGenome/N2.chr22.unsorted.bam \
    --sampletag D1.chr22.fastq,D2.chr22.fastq,N1.chr22.fastq,N2.chr22.fastq \
    --processtag Raw,Unique,Trim,rRNA,smallRNA,mapGenome \
    -o ${OUT}/11.quality_control/read_map_statistics
```

#### 5.2 统计sam文件中的reads分布, D2,N1,N2都类似操作

```
icSHAPE-pipe samStatistics \
    -i ${OUT}/5.mapGenome/D1.chr22.fastq.unsorted.bam \
    -o ${OUT}/11.quality_control/D1.chr22.fastq_SamMap \
    -g ${OUT}/GTF/Anno.genomeCoor.bed \
    --fast
```

#### 5.3 基因组上的icSHAPE值的分布

```
icSHAPE-pipe plotGenomeSHAPEdist \
    -i ${OUT}/8.calcGenomeSHAPE/shape.gTab \
    -o ${OUT}/11.quality_control/GenomeSHAPEDist
```

#### 5.4 转录本上的icSHAPE值的分布

```
icSHAPE-pipe transSHAPEStatistics \
    -i ${OUT}/9.transSHAPE/final.shape \
    -g ${OUT}/GTF/Anno.genomeCoor.bed \
    -o ${OUT}/11.quality_control/transSHAPE
```

#### 5.5 icSHAPE值与已知的二级结构之间的一致程度

```
icSHAPE-pipe evaluateSHAPE \
    -i ${OUT}/9.transSHAPE/final.shape \
    -s ${STR_18S}

icSHAPE-pipe evaluateSHAPE \
    -i ${OUT}/9.transSHAPE/final.shape \
    -s ${STR_28S}
```

#### 5.6 搜索参数

```
icSHAPE-pipe trainParameter \
    -d ${STR_18S} \
    -s ${OUT}/rRNA_index/rRNA.len \
    -D ${OUT}/7.sam2tab/rRNA_D1.chr22.tab,${OUT}/7.sam2tab/rRNA_D2.chr22.tab \
    -N ${OUT}/7.sam2tab/rRNA_N1.chr22.tab,${OUT}/7.sam2tab/rRNA_N2.chr22.tab \
    -o report.pdf
```

### 6. One-line command

> 一条命令可以执行所有的操作

```
run-icpipe-lsf.sh \
    -D ${D_1},${D_2} \
    -N ${N_1},${N_2} \
    -f ${GENOME} \
    -g ${GTF} \
    -a ${HUMAN_ABUNDANT_RNA} \
    -s ensembl \
    -r ${HUMAN_ABUNDANT_RNA} \
    -o ${OUT} \
    -p 20 \
    -q Z-ZQF \
    -l 13 \
    -v | less -S -R
```