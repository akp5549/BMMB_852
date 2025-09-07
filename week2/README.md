# Week 2 Assignment: Code and Answers

## Code used:
### Create new directory for week2
```
pwd
ls
cd BMMB_852/BMMB_852/
mkdir week2
cd week2
```

### Download another file from Ensembl FTP server
``` 
wget https://ftp.ensembl.org/pub/current_gff3/ailuropoda_melanoleuca/Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3.gz
```

### Unzip file
```
gunzip Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3
```

### Count lines in the unzipped file
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | wc -l
```

### Display first few lines of the file
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | head
```

### Display last few lines of the file
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | tail
```

### Display file contents page by page
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | more
```

### Count lines containing "AONE"
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | grep AONE | wc -l
```

### Display first few lines containing "AONE"
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | grep AONE | head
```

### Display lines not containing '##sequence-'
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | grep -v '##sequence-' | head
```

### Display lines not starting with '#'
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | grep -v '#' | head
```

### Display first line not starting with '#'
```
cat Ailuropoda_melanoleuca.ASM200744v2.115.chr.gff3 | grep -v '#' | head -1
```

### Save lines not starting with '#' to melano.gff3
```
cat Monopterus_albus.M_albus_1.0.112.gff3 | grep -v '#' > melano.gff3
```

### Display first few lines of melano.gff3
``` 
cat melano.gff3 | head
```

### Display first column of melano.gff3
``` 
cat melano.gff3 | cut -f 1 | head
```

### Display first two columns of melano.gff3
``` 
cat melano.gff3 | cut -f 1,2 | head
```

### Display second column of melano.gff3 page by page
``` 
cat melano.gff3 | cut -f 2 | more
```

### Display third column of melano.gff3 page by page
``` 
cat melano.gff3 | cut -f 3 | more
```

### Display first few lines of third column of melano.gff3
``` 
cat melano.gff3 | cut -f 3 | head
```

### Sort and display unique values in third column
``` 
cat melano.gff3 | cut -f 3 | sort | uniq | head
```

### Count occurrences of unique values in third column
``` 
cat melano.gff3 | cut -f 3 | sort | uniq -c | head
```

### Count occurrences of unique values in third column
``` 
cat melano.gff3 | cut -f 3 | sort | uniq -c
```

### Sort count of unique values
``` 
cat melano.gff3 | cut -f 3 | sort | uniq -c | sort | head
```

### Sort count of unique values in descending order
``` 
cat melano.gff3 | cut -f 3 | sort | uniq -c | sort -rn | head
```

### Use custom command to sort and count unique values
``` 
cat melano.gff3 | cut -f 3 | sort-uniq-count-rank | head
```




# Assignment Answers:

## 1. Tell us a bit about the organism.
### Ailuropoda melanoleuca = Giant Panda
#### - Average lifespan: 15-20 years
#### - 95% of their diet: bamboo 
#### - Must eat 20-40 pounds a day to maintain healthy weight = 10-16 hours a day feeding
#### - Habitat: mountains of central Asia
##### Source: https://www.fws.gov/species/giant-panda-ailuropoda-melanoleuca


## 2. How many sequence regions (chromosomes) does the file contain? Does that match with the expectation for this organism?
### 21 sequence regions in this file
### - According to google and literature, Giant Pandas have 42 chromosomes: 20 autosome pairs and 1 pair of sex chromosomes, matching expectations 
#### Source: https://www.cell.com/trends/genetics/fulltext/S0168-9525(19)30196-9#:~:text=The%20giant%20panda%20genome%20comprises,a%20GC%20content%20of%2041.69%25. 


## 3. How many features does the file contain?
### 1179183 features 


## 4. How many genes are listed for this organism?
### 18615 genes


## 5. Is there a feature type that you may have not heard about before? What is the feature and how is it defined? (If there is no such feature, pick a common feature.)
### scRNA = small cytoplasmic RNA
### snRNA = small nuclear RNA
#### Source: https://useast.ensembl.org/info/genome/genebuild/biotypes.html?utm_source=chatgpt.com

## 6. What are the top-ten most annotated feature types (column 3) across the genome?
### 1. exon
### 2. CDS
### 3. biological_region
### 4. mRNA
### 5. five_prime_UTR
### 6. three_prime_UTR
### 7. gene
### 8. ncRNA_gene
### 9. snRNA
### 10. lnc_RNA


## 7. Having analyzed this GFF file, does it seem like a complete and well-annotated organism?
### Yes, this GFF file is complete and well-annotated
#### Chromosome coverage = 21 sequence regions
#### Diveristy of sequencies and transcripts included: CDS, C gene segment, J gene segment, V gene segment, exons, five_prime_UTR, three_prime_UTR, mRNA, genes, exons etc. 

## 8. Share any other insights you might note.
### This file has 1 X chromosome, missing the other sex chromosome, and does not contain any mitochondrial DNA.  


