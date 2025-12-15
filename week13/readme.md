# Week 13

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
### I did this run on this paper: https://www.ncbi.nlm.nih.gov/bioproject/PRJNA887926/ , and created a Makefile to get a count matrix using examples from class and past Makefile scripts as example

## 4. Make all
```
make all_samples NAME=staphylococcus_aureus THREADS=8
```
