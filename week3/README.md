# Week 3 Assignment: Code and Answers

## Code used:
### Create new directory for week3:
```
mkdir week3
```

### Switch to that folder and confirm location:
```
cd week3
pwd
ls
micromamba activate bioinfo
```

### Download the genome
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/bacteria/current/fasta/bacteria_109_collection/salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905/dna/Salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905.ASM551990v1_.dna.toplevel.fa.gz
```

### Unzip and rename file
```
gunzip Salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905.ASM551990v1_.dna.toplevel.fa.gz
mv Salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905.ASM551990v1_.dna.toplevel.fa salm-enter.fa
```

### Examine the file:
```
seqkit stats salm-enter.fa
cat salm-enter.fa | grep ">"
grep -v ">" salm-enter.fa | tr -d '\n' | wc -c
```

### Download the gff3 file:
```
wget https://ftp.ensemblgenomes.ebi.ac.uk/pub/bacteria/current/gff3/bacteria_109_collection/salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905/Salmonella_enterica_subsp_enterica_serovar_enteritidis_gca_005519905.ASM551990v1.62.gff3.gz
```

### Unzip gff3 file:
```
gunzip -c salm-enter.gff > salm-enter.gff3
```

### Examine gff3 file & create a new file for gene/transcripts only:
```
grep -v "^#" salm-enter.gff3 | cut -f3 | sort | uniq -c
grep -Ew "gene|transcript" salm-enter.gff3 > gene_transcript.gff
```


## HW Questions

### How big is the genome, and how many features of each type does the GFF file contain?
#### Genome size = 4707604 bp with the follow features:
#### 4423 CDS
#### 4629 exon
#### 4423 gene
#### 4423 mRNA
#### 206 ncRNA
#### 206 ncRNA_gene
#### 34 region




<img width="1918" height="1016" alt="image" src="https://github.com/user-attachments/assets/72b4aee8-4a13-4d65-96ce-aafbccd10212" />
