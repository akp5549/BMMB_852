# Week 14

## 1. Download the metadata
```
bio search --header --csv  PRJNA887926 > metadata.csv
```
## 2. Create a design.csv file
```
run_accession,sample_aliasm,group
SRR21835898,NT1,control
SRR21835899,NaP3,treatment
SRR21835896,NT3,control
SRR21835897,NT2,control
SRR21835900,NaP2,treatment
SRR21835901,NaP1,treatment
```

## 3. Makefile
### I did this run on this paper: https://www.ncbi.nlm.nih.gov/bioproject/PRJNA887926/ , and built on the Makefile from week 13

## 4. Make all
```
make all_samples NAME=staphylococcus_aureus THREADS=8
```

## 5. First 20 from counts.csv file:
```
GeneID,Chromosome,Start,End,Strand,Length,SRR21835898,SRR21835899,SRR21835896,SRR21835897,SRR21835900,SRR21835901
SAUSA300_0001,CP000255.1,544,1905,+,1362,635,551,487,564,640,531
SAUSA300_0002,CP000255.1,2183,3316,+,1134,365,363,308,287,372,373
SAUSA300_0003,CP000255.1,3697,3942,+,246,23,21,3,8,30,18
SAUSA300_0004,CP000255.1,3939,5051,+,1113,179,265,87,155,300,247
SAUSA300_0005,CP000255.1,5061,6995,+,1935,263,541,247,254,489,523
SAUSA300_0006,CP000255.1,7032,9695,+,2664,269,427,196,264,440,432
SAUSA300_0007,CP000255.1,9782,10612,-,831,36,2,22,14,28,16
SAUSA300_0008,CP000255.1,10920,12434,+,1515,470,386,361,476,380,415
SAUSA300_0009,CP000255.1,12813,14099,+,1287,314,184,536,448,159,165
SAUSA300_0010,CP000255.1,14749,15444,+,696,2,10,2,2,2,16
SAUSA300_0011,CP000255.1,15441,15770,+,330,8,11,14,6,9,6
SAUSA300_0012,CP000255.1,16133,17101,+,969,217,227,174,183,281,230
SAUSA300_0013,CP000255.1,17392,18330,+,939,151,492,86,143,503,491
SAUSA300_0014,CP000255.1,18345,20312,+,1968,585,2209,636,547,2449,2595
SAUSA300_0015,CP000255.1,20309,20755,+,447,314,1025,375,303,933,1113
SAUSA300_0016,CP000255.1,20787,22187,+,1401,628,1719,551,620,1682,1996
SAUSA300_0017,CP000255.1,22465,23748,+,1284,74,45,54,110,62,48
SAUSA300_0020,CP000255.1,24952,25653,+,702,147,224,124,140,271,264
SAUSA300_0021,CP000255.1,25666,27492,+,1827,758,1174,924,844,1035,1234
```
## 6. Discussion:  
### Consistently expressed genes
SAUSA300_0001: counts 487–640 across all six SRRs → stable expression.
SAUSA300_0012: counts 174–281 → moderate, consistent coverage.

### Low-expression genes
SAUSA300_0010: counts 2–16 → very low expression.
Variable-expression / potential differential expression genes
SAUSA300_0014: counts in controls 585, 636, 547 vs treatments 2209, 2449, 2595 → clear difference between conditions.

<img width="1918" height="1013" alt="image" src="https://github.com/user-attachments/assets/8aff7a94-9bbb-420a-be14-0b0708d9ded1" />

<img width="1916" height="1021" alt="image" src="https://github.com/user-attachments/assets/f7fdb907-556b-47f5-9677-056e82334b37" />

<img width="1918" height="1021" alt="image" src="https://github.com/user-attachments/assets/80d89593-fde4-478a-8f78-bd67848d0368" />


## 7. RNA-seq, PCA, heatmap, functional enrichment analysis
### I had to run this separately as an R script in RStudio through PSU roar collab.
