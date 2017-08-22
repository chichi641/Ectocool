# Chi EctoCooler Pipeline

# Background
## adapted tutorial from: http://angus.readthedocs.io/en/2017/variant-calling.html

## data: https://osf.io/jzpxn/


# Loading modules
## Command lines to load tools to run BWA, Samtools, and Bedtools
```
module load bwa/0.7.15 samtools/0.1.18 bedtools/2.17.0
module list
```

# Make directories
```
mkdir work
mkdir data
```

# Loading Reference ectocooler WT genome and Contigs for Mutant 
```
cd ~/work
curl -L -o EctoCoolerWT_spades_scaffolds.fasta https://osf.io/27cvw/download
cd ~/data
curl -L -o Ecto_Orange_CGTACGTA_L001_R1_001.fastq https://osf.io/26euz/download
curl -L -o Ecto_Orange_CGTACGTA_L001_R2_001.fastq https://osf.io/95brw/download
```

# Running BWA
## Indexing ectoooler contigs in BWA
```
cd ~/work
bwa index EctoCoolerWT_spades_scaffolds.fasta
```

## Mapping 
``` 
bwa mem -t 6 EctoCoolerWT_spades_scaffolds.fasta ~/data/Ecto_Orange_CGTACGTA_L001_R1_001.fastq ~/data/Ecto_Orange_CGTACGTA_L001_R2_001.fastq > ectoorangemutant.sam
```


# Running Samtools
## Converting SAM to BAM files 
```
cd ~/work
samtools view -hSbo ectoorangemutant.bam ectoorangemutant.sam
```

## Sorting BAM files by position in genome
```
cd ~/work
samtools sort ectoorangemutant.bam ectoorangemutant.sorted  
```


## Indexing the BAM file so that we can randomly access it quickly
```
cd ~/work
samtools index ectoorangemutant.sorted.bam 
```


## Viewing files
```
samtools tview ectoorangemutant.sorted.bam ectocoolerWT_spades_scaffolds.fasta
```

* left and right arrows scroll
* q to quit
* CTRL-h and CTRL-l do “big” scrolls
* g ecoli:3931002 will take you to a specific location

## Calling variants
```
samtools mpileup -uD -f EctoCoolerWT_spades_scaffolds.fasta ectoorangemutant.sorted.bam |  \
    bcftools view -bvcg - > ectoorange_variants.raw.bcf
```

## Viewing variant
```
bcftools view ectoorange_variants.raw.bcf > ectoorange_variants.vcf
```

## Looking at uncommented header
```
grep -v ^## ectoorange_variants.vcf
```

## list five column
```
grep -v ^## ectoorange_variants.vcf | less -S
```



## Get ectocoolor annotation
```
cd ~/work
curl -L -o ectocooler.anno.tar.gz https://osf.io/3ebwc/download

tar -xvzf ectocooler.anno.tar.gz
```

# Running Bedtools
## Bedtools intersect
```
bedtools intersect -a anno.ectocooler/ectocooler.gff -b ectoorange_variants.vcf -wa -u
```

## Extracting read with Samtools
```
samtools view ectoorangemutant.sorted.bam > out.sam
 wc -l out.sam
 ```
 


## To look at list of mutations
```
less ectoorange_variants.vcf
```

## To view in T view
```
samtools tview ectoorangemutant.sorted.bam EctoCoolerWT_spades_scaffolds.fasta -p ectocooler:81910
```

Examples 

contig name: position 

NODE_1_length_115824_cov_6.29478:103837

NODE_10_length_59305_cov_5.28522: 33582 

NODE_22_length_42760_cov_5.69343: 37947
good

NODE_40_length_33068_cov_5.35485: 1001
good


NODE_53_length_23788_cov_5.99725: 1302
good


NODE_104_length_8518_cov_5.16601: 105

NODE_156_length_523_cov_0.878788: 181
good


NODE_309_length_435_cov_0.970779: 225

NODE_223_length_473_cov_0.901734: 177

NODE_197_length_486_cov_0.844011: 452

NODE_134_length_2182_cov_11.3499: 1995
good


NODE_127_length_2996_cov_48.8393: 2813
maybe 



NODE_114_length_5909_cov_4.05396: 5810
good


NODE_62_length_19742_cov_5.85633: 19628


# Helpful commands to view files
## number of lines in file
```
cat ectoorange_variants.vcf | wc -l
```


## view variant
```
bcftools view ectoorange_variants_depth350.raw.bcf > ectoorange_variants_deph350.vcf
```

## look at uncommented header
```
grep -v ^## ectoorange_variants_depth350.vcf
```

## list five column
```
grep -v ^## ectoorange_variants_deph500.vcf | less -S
```

## To look at list of mutations
```
less ectoorange_variants_deph350.vcf
```

## number of lines in file
```
cat ectoorange_variants_deph350.vcf | wc -l
```


## getting files off of server, log out of server
```
scp chnguyen@class.mbl.edu:/class/chnguyen/work/ectoorange_variants.vcf /Users/chinguyen641/desktop/
```


copy and past to Github and save as .md file
put link in report for rebecca 


