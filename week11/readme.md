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

<img width="1122" height="533" alt="image" src="https://github.com/user-attachments/assets/316a82f3-e5b0-4d81-8113-2de0bc735c1f" />


## 8. Merge VCF files
```
bcftools merge -O z VCF/*.vcf.gz -o all_variants.vcf.gz
```

## 9. IGV 
<img width="1912" height="415" alt="image" src="https://github.com/user-attachments/assets/228a2dbf-3728-410b-a3cc-abb15c4e43a6" />


## 10. Run snpEff on VCF file:
### Added to makefile, then run
```
make snpeff_run
```


