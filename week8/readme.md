# Week 8: Automate a large-scale analysis 

## 1. Identify sample names that connect the SRR numbers to the samples

## 2. Create a design.csv file that connects the SRR numbers to the sample names

```
cat design.csv | \
    parallel --dry-run --colsep , --header : --eta --lb -j 10 \
             make \
             SRR={Run} \
             SAMPLE={Sample} \
             GROUP={Group} \
             CONDITION={Condition} \
             all
```

## 3. Create Makefile that can produce multiple BAM alignment files/ use one from last week

## 4. Using GNU parallel run the Makefile on all (or at least 10) samples

