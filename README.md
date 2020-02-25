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

# Indexing 
```
preparation of annotation file of chromosome 6 from GFF3 to GTF 
we used genometools first which shows a GTF2.2 having no gene id, only transcripts and gene id which wasn't specific for further feature count so we used gffreads tools after 
```
# Alignment 
```
we aligned data using Hisat and these were the output of the four files (two samples and two controls)
The ESR1 gene we are looking for appeared to be on Forward strand, that's why we focused on F in the command line 
```
## Control REP1 hs_REP1_T47D_shCTRL_RNAseq
23830798 reads; of these:
  23830798 (100.00%) were unpaired; of these:
    22173133 (93.04%) aligned 0 times
    1534826 (6.44%) aligned exactly 1 time
    122839 (0.52%) aligned >1 times
6.96% overall alignment rate

## Control REP2 hs_REP2_T47D_shCTRL_RNAseq
28344397 reads; of these:
  28344397 (100.00%) were unpaired; of these:
    26179388 (92.36%) aligned 0 times
    2094683 (7.39%) aligned exactly 1 time
    70326 (0.25%) aligned >1 times
7.64% overall alignment rate

## Sample REP1 hs_REP1_T47D_shNR2F2_RNAseq
26758033 reads; of these:
  26758033 (100.00%) were unpaired; of these:
    24732996 (92.43%) aligned 0 times
    1951113 (7.29%) aligned exactly 1 time
    73924 (0.28%) aligned >1 times
7.57% overall alignment rate

## Sample REP2 hs_REP2_T47D_shNR2F2_RNAseq
8162158 reads; of these:
  8162158 (100.00%) were unpaired; of these:
    7541703 (92.40%) aligned 0 times
    598585 (7.33%) aligned exactly 1 time
    21870 (0.27%) aligned >1 times
7.60% overall alignment rate
