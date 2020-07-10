# raw_data-process
## 质量控制的由来
#### 质量问题通常来源于测序本身或前面的文库制备。它们包括可信度低的碱基、序列特异性的偏差、3'/5'位置偏差、聚合酶链式反应（PCR）假象、未修剪的接头，以及序列污染。这些问题可能影响参考作图、组装及表达的估计，这些问题很多可以通过过滤、修剪被矫正。
## 查看数据质量
### fastqc安装
>      conda install fastqc
      

### FastQC参数：
##### -o --outdir:输出路径
##### --extract：结果文件解压缩
##### --noextract：结果文件压缩
##### -f --format:输入文件格式.支持bam,sam,fastq文件格式
##### -t --threads:线程数
##### -c --contaminants：制定污染序列。文件格式 name[tab]sequence
##### -a --adapters：指定接头序列。文件格式name[tab]sequence
##### -k --kmers：指定kmers长度（2-10bp,默认7bp）
##### -q --quiet： 安静模式
##### 
### 运行命令
>     fastqc -t 4 -o fastqc sample1_1.fq sample1_2.fq

### fastqc结果查看
#### 1. 产生两个结果文件：
##### html：网页版结果
##### zip：本地结果压缩文件
### 2.需要重点关注的结果：
##### * Basic Statistics：**对数据量的概览
##### 
##### * Per base sequence quality：reads每个位置测序质量最直接的展示
##### 
##### * Per sequence quality scores：总体reads测序质量趋势
##### 
##### * Per base sequence content：ATGC含量估计测序是否存在偏差
##### * Sequence Duplication Levels]：影响测序的因素太多，查看是否存在污染，数据处理时是否需要去冗余；现在数据量都可以满足需求，因此前期数据处理时，尽量高标准，严格质控。
### 3. 查看网页版结果
##### 网页版结果页面左上角是一个summary:

![9c2d15616fe8395704f44c5e16b1e0cd.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p225)
#### 各种颜色是各项标准分析结果：绿色代表"PASS"；
#### 黄色代表"WARN"；红色代表"FAIL"。
![b107c0cef5d28877269bac307f79a53d.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p226)
![a26084a92ea1da865ec8f3a55fa97659.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p227)
### reads上每个位置碱基质量
##### 质量分数使用Fred quality，Q=-10*log10(p)，p为碱基测序错误概率。
##### 横轴碱基的位置，纵轴是质量分数。红色表示中位数，黄色是25%-75%区间，触须是10%-90%区间，蓝线是平均数。
##### 平均每个碱基的测序质量boxplot下四分位线在30分以上，则认为测序质量非常好；一般情况下，reads首尾质量较差。
##### 若任一位置的下四分位数低于10或中位数低于25，报"WARN"；
##### 若任一位置的下四分位数低于5或中位数低于20，报"FAIL"。
![a4a1aff1bf1324349a4e4042c41d0250.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p228)
##### 检查reads中每一个碱基位置在不同的测序小孔之间的偏离度，蓝色表示低于平均偏离度，偏离度小，质量好；越红表示偏离平均质量越多，质量也越差。如果出现质量问题可能是短暂的，如有气泡产生，也可能是长期的，如在某一小孔中存在残骸，问题不大。
![f4dddb833963120d69cc6601e49bbd61.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p229)
### 每条序列的测序质量分布
##### 横轴为序列测序质量，纵轴是reads数目。一般认为90%的reads测序质量在35分以上，则认为该测序质量非常好。
##### 当测序质量峰值小于27（错误率0.2%）时报"WARN";
##### 当峰值小于20（错误率1%）时报"FAIL"。
![97cbb0f6be26948ca0cd126f3e47fdcb.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p230)
### 统计reads每个位置ATCG四种碱基的分布：
##### 横轴为碱基位置，纵轴为百分比。因为随机的文库中，正常情况下所有位置出现某种碱基的概率是相近的，因此好的测序结果中四条线应该平行且接近。当部分位置碱基的比例出现bias时，即四条线在某些位置纷乱交织，往往提示我们有overrepresented sequence的污染。当所有位置的碱基比例一致的表现出bias时，即四条线平行但分开，往往代表文库有bias (建库过程或本身特点)，或者是测序中的系统误差。
##### 当任一位置的A/T比例与G/C比例相差超过10%，报"WARN"；
##### 当任一位置的A/T比例与G/C比例相差超过20%，报"FAIL"。
![ed702c61bfe3a81634a12c7d44c9cd06.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p231)
### 统计reads的平均GC含量分布
##### 红线是实际情况，蓝线是理论分布（正态分布，均值不一定在50%，而是由平均GC含量推断的）。 曲线形状的偏差往往是由于文库的污染或是部分reads构成的子集有偏差（overrepresented reads）。形状接近正态但偏离理论分布的情况提示我们可能有系统偏差。
##### 偏离理论分布的reads超过15%时，报"WARN"；偏离理论分布的reads超过30%时，报"FAIL"。
![44618d44f0572d19205099be691cc9fa.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p232)
### 统计reads每个位置N的比率
##### reads某个位置无法确定是何种碱基时，使用N代替；
##### 正常情况下，N的比例是很小的，所以图上常常看到一条直线，但放大Y轴之后会发现还是有N的存在，这不算问题。当Y轴在0%-100%的范围内也能看到“鼓包”时，说明测序系统出了问题。
##### 当任意位置的N的比例超过5%，报"WARN"；
##### 当任意位置的N的比例超过20%，报"FAIL"。
![c0f5bb97aa0932e2925f5378ff5b5c10.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p233)
### reads长度分布
##### 为了防止建库或者测序时有一些不规则长度的序列也被进行测序而进行的一个对长度的统计，当所有序列的长度不一样，fastqc就会警告。
##### 当reads长度不一致时报"WARN"；
##### 当有长度为0的read时报“FAIL”。
![d19c8aaff920f5df6e029616b82fc86d.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p234)
### 统计reads重复水平
##### 测序本身就会产生重复reads,测序深度越高,reads重复数越大；如果重复出现峰值，就提示可能b存在偏差（如建库过程中的PCR duplication）。
##### 横坐标是重复的次数，纵坐标是duplicated reads占unique reads种数百分比。
##### fastqc抽取reads文件前200,000条reads统计其重复情况。重复数目大于等于10的reads被合并统计，这也是为什么我们看到上图的最右侧略有上扬。大于75bp的reads只取50bp进行比较。由于reads越长错误率越高，所以其重复程度仍有可能被低估。
##### 当非unique的reads占总数的比例大于20%时，报"WARN"；
##### 当非unique的reads占总数的比例大于50%时，报"FAIL“。
![16bfdfe92e49a9d6ae629e170119bff6.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p235)
### 过度重复出现的序列的统计信息（此次没有）
![2cad61d6c54cacf20ff637490022750d.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p236)
### Adapter序列在reads中出现概率
##### 接头序列统计，>5%时是Warning，>10%时是Failure。
![d8953bc4aa066ea840824bfeb28da1b8.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p237)
![4457a9e7508de27740482446e9f6fc66.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p238)
### 过度重复的短序列统计
##### Kmer意为连指定长度为K的序列，默认K=7，取值范围2-10bp。
##### 取前2%的序列进行统计，序列长度超过500bp的截取500bp来计算。
## Trimmomatic
#### 简介 
    trimmomatic是一款用来处理illumina测序数据的工具，可以是单条的single reads，也可以是成对的pairend reads。支持压缩格式数据。功能主要包括：
        1、去除adapter序列以及测序中其它特殊序列；
        2、采用滑动窗口的方法，切除或者删除低质量碱基；
        3、去除头部低质量以及N碱基过多的reads；
        4、去除尾部低质量以及N碱基过多的reads；
        5、截取固定长度的reads；
        6、丢掉小于一定长度的reads；
        7、Phred 质量值转换

#### 下载安装
```
wget http:/www.usadellab.org/cms/uploads/supplementary/Trimmomatic/Trimmomatic-0.36.zip
unzip Trimmomatic-0.36.zip
```
#### 参数介绍
```
-version 软件版本
-threads 线程数
-phred33 -phred64 质量值体系，默认是-phred64，但是目前使用的几乎都是-phred33，所以这个要设置，很多程序是可以自动识别的。
-trimlog 截取的日志文件
-basein 输入文件，可以直接是序列，也可以是reads文件，一般都是reads1和reads2
-baseout 输出文件，这里比较麻烦，如果是pairend reads，会输出四个文件，其中两个没什么用(是unpaired reads)。SE比较简单，只有一个过滤后的输出文件。
```
除了软件中列出这些选项参数，还有很多没列出来，比如很多调节参数，滑动窗口大小，质量值大小，最小序列长度等，这些都需要通过关键字加上冒号的方法来设置，很不方便。
```
ILLUMINACLIP: 过滤 reads 中的 Illumina 测序接头和引物序列，并决定是否去除反向互补的 R1/R2 中的 R2
SLIDINGWINDOW: 从 reads 的 5' 端开始，进行滑窗质量过滤，切掉碱基质量平均值低于阈值的滑窗
MAXINFO: 一个自动调整的过滤选项，在保证 reads 长度的情况下尽量降低测序错误率，最大化 reads 的使用价值
LEADING: 从 reads 的开头切除质量值低于阈值的碱基
TRAILING: 从 reads 的末尾开始切除质量值低于阈值的碱基
CROP: 从 reads 的末尾切掉部分碱基使得 reads 达到指定长度
HEADCROP: 从 reads 的开头切掉指定数量的碱基
MINLEN: 如果经过剪切后 reads 的长度低于阈值则丢弃这条 reads
AVGQUAL: 如果 reads 的平均碱基质量值低于阈值则丢弃这条 reads
TOPHRED33: 将 reads 的碱基质量值体系转为 phred-33
TOPHRED64: 将 reads 的碱基质量值体系转为 phred-64
```



#### 使用案例：

##### 案例一：single_end情况
```
java -jar trimmomatic-0.35.jar SE -phred33 input.fq.gz output.fq.gz
```
##### 案例二：pair-end情况
```
java -jar trimmomatic-0.35.jar PE -phred33 input_forward.fq.gz input_reverse.fq.gz output_forward_paired.fq.gz output_forward_unpaired.fq.gz output_reverse_paired.fq.gz ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```
## Trim galore
#### 简介  
    Trim Galore是对FastQC和Cutadapt的包装。适用于所有高通量测序，包括RRBS(Reduced Representation Bisulfite-Seq ), Illumina、Nextera 和smallRNA测序平台的双端和单端数据。主要功能包括两步：第一步首先去除低质量碱基，然后去除3'末端的adapter, 如果没有指定具体的adapter，程序会自动检测前1million的序列，然后对比前12-13bp的序列是否符合以下类型的adapter:
            Illumina:  AGATCGGAAGAGC
            Small RNA: TGGAATTCTCGG
            Nextera:   CTGTCTCTTATA
 #### 安装
    conda install trim-galore
 #### 参数介绍
    --quality：设定Phred quality score阈值，默认为20。
    --phred33：选择-phred33或者-phred64，表示测序平台使用的Phred quality score。
    --adapter：输入adapter序列。也可以不输入，Trim Galore!会自动寻找可能性最高的平台对应的adapter。自动搜选的平台三个，也直接显式输入这三种平台，即--illumina、--nextera和--small_rna。
    --stringency：设定可以忍受的前后adapter重叠的碱基数，默认为1（非常苛刻）。可以适度放宽，因为后一个adapter几乎不可能被测序仪读到。
    --length：设定输出reads长度阈值，小于设定值会被抛弃。
    --paired：对于双端测序结果，一对reads中，如果有一个被剔除，那么另一个会被同样抛弃，而不管是否达到标准。
    --retain_unpaired：对于双端测序结果，一对reads中，如果一个read达到标准，但是对应的另一个要被抛弃，达到标准的read会被单独保存为一个文件。
    --gzip和--dont_gzip：清洗后的数据zip打包或者不打包。
    --output_dir：输入目录。需要提前建立目录，否则运行会报错。
    --trim-n : 移除read一端的reads
#### 使用案例
##### 案例一：single-end情况
    trim_galore -q 25 --phred33 --length 36 --stringency 3  -o ./ SRR2016976_1.fastq
##### 案例二：pair-end情况
    trim_galore -q 25 --phred33 --length 36 --stringency 3 --paired -o $dir  $fq1 $fq2







## sickle
#### 简介
    sickle不能用于adapter序列的切除，它没有这个功能。
#### 使用参数
```
pe	paired-end sequence trimming
se	single-end sequence trimming
Paired-end separated reads
--------------------------
-f, --pe-file1, Input paired-end forward fastq file (Input files must have same number of records)
-r, --pe-file2, Input paired-end reverse fastq file
-o, --output-pe1, Output trimmed forward fastq file
-p, --output-pe2, Output trimmed reverse fastq file. Must use -s option.

Paired-end interleaved reads
----------------------------
-c, --pe-combo, Combined (interleaved) input paired-end fastq
-m, --output-combo, Output combined (interleaved) paired-end fastq file. Must use -s option.
-M, --output-combo-all, Output combined (interleaved) paired-end fastq file with any discarded read written to output file as a single N. Cannot be used with the -s option.
```
#### 使用案例
##### 案例一：single-end情况
    sickle se -f raw_data/untreated.fq -t sanger -o u.sickle.fq
##### 案例二：pair-end情况
    sickle pe -f SRR2016976_1.fastq -r SRR2016976_2.fastq.gz -t sanger -o SRR2016976_1.sickle.fq -p SRR2016976_2.sickle.fq -s SRR2016976_1.trim.fq


## scythe
    scyth不能自动识别接头序列，不输入接头序列文件无法运行该程序。所以我认为该程序不适用于处理多个不同平台的测序数据。

* * *


## 软件比较
```R
#BiocManager::install("qrqc")
library(qrqc)

# 指定fastq文件路径
fq_files = c(rawdata="NPC/SRR2016976_1.fastq",
             scikle = "NPC/SRR2016976_1.sickle.fq",
             trim_galore="clean/trim_galore/SRR2016976_1_val_1.fq",
             trimmomatic="clean/trimmomatic/SRR2016976_1P.fastq")
             
# Load each fastq file in by using qrqc's readSeqFile
seq_read = lapply(fq_files, function(file) {
  readSeqFile(file, hash = FALSE, kmer = FALSE)
})

quals = mapply(function(sfq, name) {
  qs = getQual(sfq)
  qs$trimmer = name
  qs
}, seq_read, names(fq_files), SIMPLIFY = FALSE)

d = do.call(rbind, quals)

# 画图
library(ggplot2)
position = d$position
mean = d$mean
trimmer = d$trimmer
p1 = qplot(position, mean, color=trimmer, geom=c("line", "point"))
# p1 = ggplot(d) + geom_line(aes(x=position, y=mean, linetype=trimmer))
# p1 = ggplot(d) + geom_line(aes(x=position, y=mean, color=trimmer))
p1 = p1 + ylab("Mean quality (Phred33)") + theme_bw()
print(p1)

p2 = qualPlot(seq_read, quartile.color = NULL, mean.color = NULL) + theme_bw()
p2 = p2 + scale_y_continuous("Quality (Phred33)")
print(p2)
```
这段代码接受4个输入文件，一份原始的fastq和其他3份修剪过后的fastq，并输出两幅图：图1和图2。

在图1中，我们同时对比了这4份文件的质量值分布情况，可以看出，无论是用trimmomatic、sickle还是trim galore我们都将read末尾的低质量碱基成片切除了，末端的质量值范围也会被我们限制在了较高的区域，这可以确保下游分析过程不会过多地被低质量碱基所干扰。
![7e12fa9cf14466c93e5f022086f9ec39.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p241)
图1.质量值分布

在图2中，横轴是read的位置，纵轴是各位置上的平均质量值，我们可以看到越到read的末尾，质量会随着下降，特别是没做任何修剪的原始fastq，可以看出其末尾的碱基质量更差，这意味着错误率更高了。这里trimmomatic和trim_galore的结果比较好一点。
![a6bcf777ab71374d5b6d26961dfabd1a.png](evernotecid://7BBD62DF-1439-4EBB-8D8F-D85D5140390E/appyinxiangcom/22069768/ENResource/p240)
图2. 平均质量值分布

### 小结
从上面的比较结果中，我们可以明显发现，trimmomatic最优，trim galore次之，scikle第三。这其中结果最好的Trimmomatic丢弃掉的数据要比其他的略多（约1%~2%）,而且trimmomartic占用内存较高，在电脑内存不高的情况下，可以考虑使用trim galore作为替代。
