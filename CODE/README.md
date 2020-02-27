## Install [bioconda](https://docs.conda.io/en/latest/miniconda.html) if you don't have it already

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
## restart the terminal
conda config --add channels r
conda config --add channels conda-forge
conda config --add channels bioconda
```

## create a new enviornment
```
conda create -y --name ngs2 python=3.6
conda activate ngs2
```

# Data preparation 
## Download
mkdir -p ~/Desktop/PROJECT/SAMPLE_DATA && cd ~/Desktop/PROJECT/SAMPLE_DATA
# Control Data
```
wget https://sra-download.ncbi.nlm.nih.gov/traces/sra46/SRZ/010958/SRR10958001/hs_T47D_shCTRL_RNAseq_rep1.fastq.gz
wget https://sra-download.ncbi.nlm.nih.gov/traces/sra43/SRZ/010958/SRR10958009/hs_T47D_shCTRL_RNAseq_rep2.fastq.gz
```
# Sample Data 
```
wget https://sra-download.ncbi.nlm.nih.gov/traces/sra54/SRZ/010958/SRR10958000/hs_T47D_shNR2F2_RNAseq_rep1.fastq.gz
wget https://sra-download.ncbi.nlm.nih.gov/traces/sra43/SRZ/010958/SRR10958005/hs_T47D_shNR2F2_RNAseq_rep2.fastq.gz
```
# Reference file
```
wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.chromosome.6.fa.gz
wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.chromosome.15.fa.gz
```
# Annotation file
```
wget ftp://ftp.ensembl.org/pub/release-99/gff3/homo_sapiens/Homo_sapiens.GRCh38.99.chromosome.6.gff3.gz
wget ftp://ftp.ensembl.org/pub/release-99/gff3/homo_sapiens/Homo_sapiens.GRCh38.99.chromosome.15.gff3.gz
```
# Preparation of GTF file
```
>> first we used genometools for conversion of gff to gtf #but unfortunately it showed at GTF2.2 version which only has transcript and exon ids
gunzip Homo_sapiens.GRCh38.99.chromosome.15.gff3.gz
#gt gff3_to_gtf Homo_sapiens.GRCh38.99.chromosome.6.gff > Homo_sapiens.GRCh38.99.chromosome.15.gtf 
>>so we switched to using 
conda install gffcompare
gffread Homo_sapiens.GRCh38.99.chromosome.15.gff3 -T -o Homo_sapiens.GRCh38.99.chromosome.15.gtf
```

# Fastq Quality Check 
```
# Installing Software 
conda install -c bioconda fastqc 
conda install -c bioconda multiqc  
```
# BEFORE TRIMMING QC 
```
mkdir -p ~/Desktop/PROJECT/FastaQC && cd ~/Desktop/PROJECT/FastaQC 
# Run the FASTQC for each read end
for f in ~/Desktop/PROJECT/SAMPLE_DATA/*.fastq.gz;do fastqc -t 1 -f fastq -noextract $f;done
mv ../Sample_data/*html ./
mv ../Sample_data/*zip ./
```

# UNTRIMMED DATA ANALYSIS
```
mkdir -p ~/Desktop/PROJECT/Hisat/Hisat_align/HisatIndex && cd ~/Desktop/PROJECT/Hisat/Hisat_align/HisatIndex
```
# INDEXING using HISAT2
```
conda install -c bioconda -y hisat2
mkdir -p ~/PROJECT/Hisat/hisat_align/hisatIndex && cd ~/PROJECT/Hisat/hisat_align/hisatIndex
ln -s ~/Desktop/PROJECT/SAMPLE_DATA/Homo_sapiens.GRCh38.dna.chromosome.15.fa .
hisat2_extract_splice_sites.py ~/Desktop/PROJECT/SAMPLE_DATA/Homo_sapiens.GRCh38.99.chromosome.15.gtf > splicesites.tsv
hisat2_extract_exons.py ~/Desktop/PROJECT/SAMPLE_DATA/Homo_sapiens.GRCh38.99.chromosome.15.gtf > exons.tsv
hisat2-build -p 1 --ss splicesites.tsv --exon exons.tsv Homo_sapiens.GRCh38.dna.chromosome.15.fa Homo_sapiens.GRCh38
```
# ALIGNMENT of UNTRIMMED DATA using HISAT2
```
cd ~/Desktop/PROJECT/Hisat/hisat_align/
newfile="~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shNR2F2_RNAseq_rep1.fastq.gz"
hisat2 -p 1 -x hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile -S hs_T47D_shNR2F2_RNAseq_rep1.sam

newfile1="~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shNR2F2_RNAseq_rep2.fastq.gz"
hisat2 -p 1 -x hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile1 -S hs_T47D_shNR2F2_RNAseq_rep2.sam

newfile2="~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shCTRL_RNAseq_rep1.fastq.gz"
hisat2 -p 1 -x hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile2 -S hs_T47D_shCTRL_RNAseq_rep1.sam

newfile3="~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shCTRL_RNAseq_rep2.fastq.gz"
hisat2 -p 1 -x hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile3 -S hs_T47D_shCTRL_RNAseq_rep2.sam
```
# Preparing SAM/BAM/SORTED_BAM files
```
conda install -y samtools
#convert SAM file to BAM file
samtools view -bS hs_T47D_shCTRL_RNAseq_rep1.sam > hs_T47D_shCTRL_RNAseq_rep1.bam
samtools view -bS hs_T47D_shCTRL_RNAseq_rep2.sam > hs_T47D_shCTRL_RNAseq_rep2.bam
samtools view -bS hs_T47D_shNR2F2_RNAseq_rep1.sam > hs_T47D_shNR2F2_RNAseq_rep1.bam
samtools view -bS hs_T47D_shNR2F2_RNAseq_rep2.sam > hs_T47D_shNR2F2_RNAseq_rep2.bam

#convert the BAM file to a sorted BAM file
samtools sort hs_T47D_shCTRL_RNAseq_rep1.bam -o hs_T47D_shCTRL_RNAseq_rep1.sorted.bam
samtools sort hs_T47D_shCTRL_RNAseq_rep2.bam -o hs_T47D_shCTRL_RNAseq_rep2.sorted.bam
samtools sort  hs_T47D_shNR2F2_RNAseq_rep1.bam -o hs_T47D_shNR2F2_RNAseq_rep1.sorted.bam
samtools sort  hs_T47D_shNR2F2_RNAseq_rep2.bam -o hs_T47D_shNR2F2_RNAseq_rep2.sorted.bam
```
# Visualization of Data using IGV
## Indexing the BAM file
```
samtools index hs_T47D_shCTRL_RNAseq_rep1.sorted.bam
samtools index hs_T47D_shCTRL_RNAseq_rep2.sorted.bam
samtools index hs_T47D_shNR2F2_RNAseq_rep1.sorted.bam
samtools index hs_T47D_shNR2F2_RNAseq_rep2.sorted.bam
```

# Differential Expression
## Preparing enviroment
```
wget -c https://raw.githubusercontent.com/mr-eyes/nu-ngs01/master/Day-6/deseq1.r
wget -c https://raw.githubusercontent.com/mr-eyes/nu-ngs01/master/Day-6/draw-heatmap.r
conda install subread
conda install r
conda install -y bioconductor-deseq r-gplots
```
## Quantification
```
GTF=/home/zainabkamel/Desktop/PROJECT/SAMPLE_DATA/Homo_sapiens.GRCh38.99.chromosome.15.gtf
featureCounts -a $GTF -g gene_id -o counts.txt  hs_T47D_shCTRL_RNAseq_rep*.bam  hs_T47D_shNR2F2_RNAseq_rep*.bam
cat counts.txt | cut -f 1,7-10 > simple_counts.txt
```
### Analyze the counts with Deseq
```
cat simple_counts.txt | Rscript deseq1.r 2x2 > results_deseq1.tsv
```
### View only rows with pval < 0.05
```
cat results_deseq1.tsv | awk ' $8 < 0.05 { print $0 }' > filtered_results_deseq1.tsv
cat filtered_results_deseq1.tsv | Rscript draw-heatmap.r > hisat_output.pdf
```

# Data Trimming 
```
conda install -c bioconda trimmomatic 
mkdir -p ~/Desktop/PROJECT/trimmomatic && cd ~/Desktop/PROJECT/trimmomatic
#At first we croped the first 13 basepair by using Headcrop:13 
trimmomatic SE -phred33 ~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shCTRL_RNAseq_rep1.fastq.gz results.fq.gz ILLUMINACLIP:TRUSeq-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 HEADCROP:13
#still we have warning after QC so we added crop:61 to cut the end too 

trimmomatic SE -phred33 ~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shCTRL_RNAseq_rep1.fastq.gz hs_T47D_shCTRL_RNAseq_rep1.fq.gz ILLUMINACLIP:TRUSeq-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 HEADCROP:13 CROP:61

trimmomatic SE -phred33 ~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shCTRL_RNAseq_rep2.fastq.gz hs_T47D_shCTRL_RNAseq_rep2.fq.gz ILLUMINACLIP:TRUSeq-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 HEADCROP:13 CROP:61

trimmomatic SE -phred33 ~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shNR2F2_RNAseq_rep1.fastq.gz hs_T47D_shNR2F2_RNAseq_rep1.fq.gz ILLUMINACLIP:TRUSeq-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 HEADCROP:13 CROP:61

trimmomatic SE -phred33 ~/Desktop/PROJECT/SAMPLE_DATA/hs_T47D_shNR2F2_RNAseq_rep2.fastq.gz hs_T47D_shNR2F2_RNAseq_rep2.fq.gz ILLUMINACLIP:TRUSeq-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 HEADCROP:13 CROP:61

```
# AFTER TRIMMING QC 
```
mkdir -p ~/Desktop/PROJECT/FastaQC_TRIMMED && cd ~/Desktop/PROJECT/FastaQC_TRIMMED
# Run the FASTQC for each read end
for f in ~/Desktop/PROJECT/TRIMMED_DATA/*.fq.gz;do fastqc -t 1 -f fastq -noextract $f;done
mv ../TRIMMED_DATA/*html ./
mv ../TRIMMED_DATA/*zip ./
```
# ALIGNMENT of TRIMMED DATA using HISAT2
```
mkdir ~/Desktop/PROJECT/Hisat_TRIMMED/hisat_align_trimmed && cd ~/Desktop/PROJECT/Hisat_TRIMMED/hisat_align_trimmed
newfile="~/Desktop/PROJECT/TRIMMED_DATA/hs_T47D_shNR2F2_RNAseq_rep1.fq.gz"
hisat2 -p 1 -x ~/Desktop/PROJECT/Hisat/hisat_align/hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile -S hs_T47D_shNR2F2_RNAseq_rep1.sam

newfile1="~/Desktop/PROJECT/TRIMMED_DATA/hs_T47D_shNR2F2_RNAseq_rep2.fq.gz"
hisat2 -p 1 -x ~/Desktop/PROJECT/Hisat/hisat_align/hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile1 -S hs_T47D_shNR2F2_RNAseq_rep2.sam

newfile2="~/Desktop/PROJECT/TRIMMED_DATA/hs_T47D_shCTRL_RNAseq_rep1.fq.gz"
hisat2 -p 1 -x ~/Desktop/PROJECT/Hisat/hisat_align/hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile2 -S hs_T47D_shCTRL_RNAseq_rep1.sam

newfile3="~/Desktop/PROJECT/TRIMMED_DATA/hs_T47D_shCTRL_RNAseq_rep2.fq.gz"
hisat2 -p 1 -x ~/Desktop/PROJECT/Hisat/hisat_align/hisatIndex/Homo_sapiens.GRCh38 --dta --rna-strandness F -U $newfile3 -S hs_T47D_shCTRL_RNAseq_rep2.sam
```
# Preparing SAM/BAM/SORTED_BAM files
```
#convert SAM file to BAM file
samtools view -bS hs_T47D_shCTRL_RNAseq_rep1.sam > hs_T47D_shCTRL_RNAseq_rep1.bam
samtools view -bS hs_T47D_shCTRL_RNAseq_rep2.sam > hs_T47D_shCTRL_RNAseq_rep2.bam
samtools view -bS hs_T47D_shNR2F2_RNAseq_rep1.sam > hs_T47D_shNR2F2_RNAseq_rep1.bam
samtools view -bS hs_T47D_shNR2F2_RNAseq_rep2.sam > hs_T47D_shNR2F2_RNAseq_rep2.bam

#convert the BAM file to a sorted BAM file
samtools sort hs_T47D_shCTRL_RNAseq_rep1.bam -o hs_T47D_shCTRL_RNAseq_rep1.sorted.bam
samtools sort hs_T47D_shCTRL_RNAseq_rep2.bam -o hs_T47D_shCTRL_RNAseq_rep2.sorted.bam
samtools sort  hs_T47D_shNR2F2_RNAseq_rep1.bam -o hs_T47D_shNR2F2_RNAseq_rep1.sorted.bam
samtools sort  hs_T47D_shNR2F2_RNAseq_rep2.bam -o hs_T47D_shNR2F2_RNAseq_rep2.sorted.bam

```
# Visualization of Data using IGV
## Indexing the BAM file
```
samtools index
samtools index
samtools index
samtools index

```
