Week 13

## 1. Download the metadata
```
bio search --header --csv  PRJNA887926 > metadata.csv
```
## 2. Create a design.csv file


## 3. Download the reference genome
```
bio fetch NC_007793.1 --format gff > refs/staphylococcus_aureus.gff
```
## 4.Makefile

## 5. Make all
```
make all_samples NAME=staphylococcus_aureus THREADS=4
```
