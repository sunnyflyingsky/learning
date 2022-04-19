## 原理



ATAC-seq[16]通过Tn5转座酶随机插入染色质开放区域，将不受核小体保护的染色质区域(染色质开放区域) 切割下来，通常用于定位染色质开放区域。



## 特点







## pipeline

##### 质控

fastqc



##### 清洗

cutadapt



##### mapping

bowtie2, bowtie, tophat, star



##### sort and filter

samtools

~~~
samtools view -h 3_merged2_large_clean.sam |samtools sort -@ 8 > 3_merged2_large_sorted.bam
~~~

##### bam2bed

bedtools

~~~
bamToBed -i 3_merged2.fastq.sorted_bc1.bam > 3_merged2.fastq.sorted_bc1.bed
~~~

##### OCR



























