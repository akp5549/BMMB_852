# Week 9: Revisions for Week 8- Automate a large-scale analysis 

## 1. Identify sample names that connect the SRR numbers to the samples. Create a design.csv file that connects the SRR numbers to the sample names
```
Run,Sample,Group,Condition
SRR35257019,SL2014_Run1,ebola,SierraLeone
SRR1972739,SL2014_Run2,ebola,SierraLeone
SRR1972974,SL2014_Run3,ebola,SierraLeone
SRR1972966, SL2014_Run4,ebola,SierraLeone
SRR1972813, SL2014_Run5,ebola,SierraLeone
SRR1972804, SL2014_Run6,ebola,SierraLeone
SRR1972812, SL2014_Run7,ebola,SierraLeone
SRR1972805, SL2014_Run8,ebola,SierraLeone
SRR1972802, SL2014_Run9,ebola,SierraLeone
SRR1734998, SL2014_Run10,ebola,SierraLeone
```

## 2. Create a Makefile that can produce multiple BAM alignment files/ use one from last week
### - Used last week's Makefile

## 3. Using GNU parallel, run the Makefile on 10 samples
```
cat design.csv | \
    parallel --colsep , --header : --eta --lb -j 10 \
             make \
             SRR={Run} \
             SAMPLE={Sample} \
             GROUP={Group} \
             CONDITION={Condition} \
             all
```

## 4. Results and outputs include: 
### - genome name, FASTQ read data, FASTQC reports for each read, Alignments and coverage files in BAM and BW formats, statistics alignment reports for each BAM file
