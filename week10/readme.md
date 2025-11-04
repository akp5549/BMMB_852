# Week 10:  

## 1. Design.csv file
```
Run,Sample,Group,Condition
SRR1972957,SL2014_Run1,ebola,SierraLeone
SRR1972739,SL2014_Run2,ebola,SierraLeone
SRR1972967,SL2014_Run3,ebola,SierraLeone
SRR1972966,SL2014_Run4,ebola,SierraLeone
SRR1972968,SL2014_Run5,ebola,SierraLeone
SRR1972964,SL2014_Run6,ebola,SierraLeone
SRR1972812,SL2014_Run7,ebola,SierraLeone
SRR1972958,SL2014_Run8,ebola,SierraLeone
SRR1972802,SL2014_Run9,ebola,SierraLeone
SRR1734998,SL2014_Run10,ebola,SierraLeone
```

## 2. Create a Makefile that can produce multiple BAM alignment files/ use one from last week
### - Used last week's Makefile

## 3. Download reference genomes:
```
make NAME=ebola-1976 refs
```

## 4. Using GNU parallel, run the Makefile on 10 samples
```
cat design.csv | \
    parallel --colsep , --header : --eta --lb -j 2 \
        make SRR={Run} NAME=ebola-1976 SAMPLE={Sample} GROUP={Group} CONDITION={Condition} all

```

## 5. If you wanted to run a single sample:
```
make all SRR=SRR1972957 NAME=ebola-1976 ACC=AF086833
```

## 6. Results and outputs include: 
### - genome name, FASTQ read data, FASTQC reports for each read, Alignments and coverage files in BAM and BW formats, statistics alignment reports for each BAM file

<img width="1122" height="533" alt="image" src="https://github.com/user-attachments/assets/316a82f3-e5b0-4d81-8113-2de0bc735c1f" />

<img width="1297" height="850" alt="image" src="https://github.com/user-attachments/assets/a90a857b-f45f-467e-bd28-737344bd761a" />


## 8. Merge VCF files
```
bcftools merge -O z VCF/*.vcf.gz -o all_variants.vcf.gz
```

## 9. IGV 
<img width="1918" height="512" alt="image" src="https://github.com/user-attachments/assets/2d7d5044-4f2a-48bb-9572-3d14dd4c413b" />

