# Week 13

## 1. Download the metadata
```
bio search --header --csv  PRJNA887926 > metadata.csv
```
## 2. Create a design.csv file
run_accession,sample_aliasm,group
SRR21835898,NT1,control
SRR21835899,NaP3,treatment
SRR21835896,NT3,control
SRR21835897,NT2,control
SRR21835900,NaP2,treatment
SRR21835901,NaP1,treatment

## 3. Download the reference genome
```
bio fetch NC_007793.1 --format gff > refs/staphylococcus_aureus.gff
```
## 4.Makefile

## 5. Make all
```
make all_samples NAME=staphylococcus_aureus THREADS=4
```
