# NGS2-Project

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
4. Annotation file of chromosome 6 "GFF3 format" 
The links of SRA and ensembl are available in script 
```
# QC for data
```
```
we have checked the QC of all data we have downloaded and obtained a merged report for them. 
you can find the report uploaded with the name multiqc_report.html

### Analysis of report: 
we found issues with "per base sequence"  (warning appeared because lower quartile for bases was less than 10) and issues with "duplicates levels" (warning appeared because non-unique sequences make up more than 20% of total) so we trimmed data 

# Data trimming 
# Hisat Alignment
## Indexing 
```
preparation of annotation file of chromosome 6 from GFF3 to GTF 
we used genometools first which shows a GTF2.2 having no gene id, only transcripts and gene id which wasn't specific for further feature count so we used gffreads tools after 
```
## Alignment 
```
we aligned data using Hisat and these were the output of the four files (two samples and two controls)
The ESR1 gene we are looking for appeared to be on Forward strand, that's why we focused on F in the command line 
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
Counting the originated file (simple_count.txt attached above)
| Geneid        |hs_T47D_shCTRL_RNAseq_rep1.bam|hs_T47D_shCTRL_RNAseq_rep1.sorted.bam|
| ------------- | ------------- |-------------|
|ENSG00000271530| 0 |0|
|ENSG00000220212| 0 |0|
|ENSG00000217929|195|195|
|ENSG00000218577|0  |0|
|ENSG00000263667|0  |0|
|ENSG00000285578|0  |0|
|ENSG00000205653|0  |0|
|ENSG00000287265|5  |5|


