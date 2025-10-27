# Week 9: Revisions for Week 8- Automate a large-scale analysis 

## 1. Identify sample names that connect the SRR numbers to the samples. Create a design.csv file that connects the SRR numbers to the sample names
```
Run,Sample,Group,Condition
SL2014_Run1,SRR35257019,ebola,SierraLeone
SL2014_Run2,SRR1972739,ebola,SierraLeone
SL2014_Run3,SRR1972974,ebola,SierraLeone
SL2014_Run4,SRR1972966,ebola,SierraLeone
SL2014_Run5,SRR1972813,ebola,SierraLeone
SL2014_Run6,SRR1972804,ebola,SierraLeone
SL2014_Run7,SRR1972812,ebola,SierraLeone
SL2014_Run8,SRR1972805,ebola,SierraLeone
SL2014_Run9,SRR1972802,ebola,SierraLeone
SL2014_Run10,SRR1734998,ebola,SierraLeone

```

## 2. Create a Makefile that can produce multiple BAM alignment files/ use one from last week
### - Used last week's Makefile

## 3. Using GNU parallel, run the Makefile on 10 samples
```
cat design.csv | \
    parallel --colsep , --header : --eta --lb -j 2 \
             make \
             SRR={Run} \
             SAMPLE={Sample} \
             GROUP={Group} \
             CONDITION={Condition} \
             all
```

## 4. Results and outputs include: 
### - genome name, FASTQ read data, FASTQC reports for each read, Alignments and coverage files in BAM and BW formats, statistics alignment reports for each BAM file
