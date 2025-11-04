# Week 10:  

## 1. Design.csv file
```
Run,Sample,Group,Condition
SRR1972957,SL2014_Run1,ebola,SierraLeone
SRR1972739,SL2014_Run2,ebola,SierraLeone
SRR1972974,SL2014_Run3,ebola,SierraLeone
SRR1972966,SL2014_Run4,ebola,SierraLeone
SRR1972813,SL2014_Run5,ebola,SierraLeone
SRR1972804,SL2014_Run6,ebola,SierraLeone
SRR1972812,SL2014_Run7,ebola,SierraLeone
SRR1972805,SL2014_Run8,ebola,SierraLeone
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

## 5. Results and outputs include: 
### - genome name, FASTQ read data, FASTQC reports for each read, Alignments and coverage files in BAM and BW formats, statistics alignment reports for each BAM file

<img width="1122" height="533" alt="image" src="https://github.com/user-attachments/assets/316a82f3-e5b0-4d81-8113-2de0bc735c1f" />

<img width="1297" height="850" alt="image" src="https://github.com/user-attachments/assets/a90a857b-f45f-467e-bd28-737344bd761a" />


## 6. If you wanted to run a single sample:
```
make all SRR=SRR1972957 NAME=ebola-1976 ACC=AF086833
```

## 7. Merge VCF files
```
bcftools merge -O z *.vcf.gz > all_variants.vcf.gz
```
