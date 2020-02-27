# The impact of un-trimming & trimming approaches on differential expression bias

# Abstract 
```
High-throughput RNA-Sequencing is one of challenging technique for studying differential gene expression
between biological samples to detect novel upregulated and downregulated genes. Trimming is a pre-processing
step that widely used to low quality bases but still heterogeneously and unstandardized; that has an impact
to downstream analyses. In this project we have downloaded four Homo Sapiens RNAseq fasta.gz bio-samples 
from Sequence Read Archive (SRA) of bio-project (PRJNA602619).This bio-project characterized NR2F2 Orphan 
Nuclear Receptor, a member of the steroid/thyroid hormone receptor superfamily and its effect  in estrogen 
receptor alpha mediated transcriptional regulation in breast cancer cells(MCF7 and T47D) using the ChIP-seq 
and RNA-seq methods. In this project we have downloaded two control runs ; hs T47D shCTRL RNAseq and two 
NR2F2 depleted T47D cells ;hs T47D shNR2F2 RNAseq from 54 years old females that used to study the influence 
of un-trimming and trimming processes in differential expression. The Aim of this project to determine the i
mpact of both processes on subsequent genome alignment and gene expression differential bias.
```
# GitHub link
```
https://github.com/ZainabKamel/NGS2-Project
```
# Code file
```
for the code open Script file
```
# Data Download
```
1. Sample Data 
2. Control Data
3. Human Chromosome 6 "REFERENCE in fasta format"
4. Human Chromosome 15 "REFERENCE in fasta format"
5. Annotation file of chromosome 6 "GFF3 format" 
6. Annotation file of chromosome 15 "GFF3 format" 
The links of SRA and ensembl are available in script 
```
# QC for data before trimming
```
we have checked the QC of all data we have downloaded (both the control and sample)
you can find the report uploaded for each control and sample file
(ATTACHED ABOVE SCREENSHOT FOR EACH QC REPORT IN BEFORE TRIMMING QC FILES)
```
### Analysis of report: 
```
We found an ALERT regarding with **per base sequence**  whcih appeared because lower 
+quartile for bases was less than 10 and WARNING about the duplication levels, length distribution
and overrepresented sequences 
```
# Data trimming 
```
We have trimmed the data based on error in per base sequence and the below are the on screen output 

1. hs_T47D_shCTRL_RNAseq_rep1.fastq.gz
Input Reads: 23830798 Surviving: 23506538 (98.64%) Dropped: 324260 (1.36%)
TrimmomaticSE: Completed successfully
```

# Hisat Alignment for untrimmedd data 
## Indexing 
```
We first worked on chromosome 6, because we were tracking ESR-1 gene on chr 6 but after reviewing the main abstract of the study we figured out we need to work on chr 15 where NR2F2 is located. 

preparation of annotation file of chromosome 6 from GFF3 to GTF 
we used genometools first which shows a GTF2.2 having no gene id, only transcripts and gene id which wasn't specific for further feature count so we used gffreads tools after 
```
## Alignment 
```
At first, we failed to work using for loop so we aligned each sample alone, but after solving the issue of for loop we reused it for conducting the resullts "viewd in details in issue number 11"

we aligned data using Hisat and these were the output of the four files (two samples and two controls)
The NR2F2 gene we are looking for appeared to be on Forward strand, that's why we focused on F in the command line 
```
### Alignment Summary
```
## Control REP1 hs_REP1_T47D_shCTRL_RNAseq
23830798 reads; of these:
  23830798 (100.00%) were unpaired; of these:
    22636860 (94.99%) aligned 0 times
    1056038 (4.43%) aligned exactly 1 time
    137900 (0.58%) aligned >1 times
5.01% overall alignment rate

## Control REP2 hs_REP2_T47D_shCTRL_RNAseq
28344397 reads; of these:
  28344397 (100.00%) were unpaired; of these:
    26884448 (94.85%) aligned 0 times
    1377373 (4.86%) aligned exactly 1 time
    82576 (0.29%) aligned >1 times
5.15% overall alignment rate

## Sample REP1 hs_REP1_T47D_shNR2F2_RNAseq
26758033 reads; of these:
  26758033 (100.00%) were unpaired; of these:
    25388901 (94.88%) aligned 0 times
    1288608 (4.82%) aligned exactly 1 time
    80524 (0.30%) aligned >1 times
5.12% overall alignment rate

## Sample REP2 hs_REP2_T47D_shNR2F2_RNAseq
29668645 reads; of these:
  29668645 (100.00%) were unpaired; of these:
    28147110 (94.87%) aligned 0 times
    1435885 (4.84%) aligned exactly 1 time
    85650 (0.29%) aligned >1 times
5.13% overall alignment rate

```
# Differential Expression
## Quantification
### Counting the originated file (simple_count.txt attached above)
| Geneid        |hs_T47D_shCTRL_RNAseq_rep1.bam|hs_T47D_shCTRL_RNAseq_rep2.bam|hs_T47D_shNR2F2_RNAseq_rep1.bam|hs_T47D_shNR2F2_RNAseq_rep2.bam|
| ------------- | ------------- |-------------|-------------|-------------|
|ENSG00000215567| 8 |4|5|2|
|ENSG00000201241| 1 |1|1|0|
|ENSG00000258463| 0 |0|1|0|
|ENSG00000274347| 0 |0|0|0|
|ENSG00000188403| 0 |0|0|0|
|ENSG00000258706| 0 |0|0|0|
|ENSG00000259337| 0 |0|0|0|

### DESeq Output

| id         | baseMean         | baseMeanA        | baseMeanB        | foldChange       | log2FoldChange   | pval                 | padj                 | 
|------------|------------------|------------------|------------------|------------------|------------------|----------------------|----------------------| 
|ENSG00000185551|1139.47799460156|1862.50163959567|416.454349607454|0.223599453956917|-2.16101142984852|1.98779882849516e-15| 9.65399264176276e-08| 
|ENSG00000128965|172.07940920188|68.8166455576957|275.342172846064|4.00109843504677|2.00039612231184|1.40728755710827e-10| 9.39729003690993e-61 | 
|ENSG00000140465|643.314371739867|235.846844998906|1050.78189848083|4.4553570283533|2.15554104620201|1.98829913800446e-09| 9.09315472447373e-07| 
|ENSG00000128739|107.580097998159|42.8485221399427|172.311673856375|4.02141463114194|2.00770309402263|7.47024287841483e-09| 2.56229330729629e-06| 


### Heatmap Visualization of rows with pval < 0.05 
```
you can find the heatmap of the DEGs of untrimmed data attached in file entitled hisat_output.pdf
```

