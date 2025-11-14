# Week 11 

## 1. Design.csv file
```
Run,Sample,Group,Condition
SRR1972967,SL2014_Run3,ebola,SierraLeone
SRR1972966,SL2014_Run4,ebola,SierraLeone
SRR1972973,SL2014_Run8,ebola,SierraLeone
```

## 2. Create a Makefile that can produce multiple BAM alignment files/ use one from last week
### - Used last week's Makefile

## 3. Download reference genomes:
```
make NAME=ebola-1976 refs
bio fetch AF086833 --format gff > refs/ebola_mayinga_1976.gff
```

## 4. Using GNU parallel, run the Makefile on 3 samples
```
cat design.csv | \
    parallel --colsep , --header : --eta --lb -j 1 \
        make SRR={Run} NAME=ebola-1976 SAMPLE={Sample} GROUP={Group} CONDITION={Condition} all

```

## 5. If you wanted to run a single sample:
```
make all SRR=SRR1972967 NAME=ebola-1976 ACC=AF086833
```

## 6. Results and outputs include: 
### - genome name, FASTQ read data, FASTQC reports for each read, Alignments and coverage files in BAM and BW formats, statistics alignment reports for each BAM file


<img width="1213" height="650" alt="image" src="https://github.com/user-attachments/assets/8a50259b-fbd3-47c8-abb2-2bd58eba150f" />



| SRR ID     | Total Reads | Primary Reads | Mapped Reads | % Mapped (primary) | Coverage Bases | Genome Size (bp) | Avg Depth |
| ---------- | ----------- | ------------- | ------------ | ------------------ | -------------- | ---------------- | --------- |
| SRR1972967 | 1028        | 1000          | 611          | 58.3%              | 16,809         | 18,959           | 3.06x     |
| SRR1972966 | 1031        | 1000          | 645          | 64.5%              | 15,165         | 18,959           | 3.38x     |
| SRR1972973 | 1017        | 1000          | 492          | 49.2%              | 15,739         | 18,959           | 2.56x     |



## 7. Merge VCF files
```
bcftools merge -O z VCF/*.vcf.gz -o all_variants.vcf.gz
```

## 8. IGV 
<img width="1912" height="415" alt="image" src="https://github.com/user-attachments/assets/228a2dbf-3728-410b-a3cc-abb15c4e43a6" />


## 9. Run snpEff on VCF file:
### Added to makefile, then run
```
make snpeff_all

```


