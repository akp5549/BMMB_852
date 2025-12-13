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
make all SRR=SRR21835898
make all SRR=SRR21835899
make all SRR=SRR21835896
make all SRR=SRR21835897
make all SRR=SRR21835900
make all SRR=SRR21835901
```
