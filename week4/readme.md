# Group 1: Ebola

# Navigate directories and activate environment 
```
cd BMMB_852/BMMB_852/
mkdir week4
cd week4
conda activate bioinfo
```

# Get FASTA file for accession number AF086833 in FASTA format and look at file
```
efetch -db nuccore -format fasta -id AF086833 > AF086833.fa
datasets summary genome accession GCF_000848505.1 | jq
```

# Download gff3 and gtf file
```
datasets download genome accession GCF_000848505.1 \--include genome,gff3,gtf
```

# Unzip file and navigate to file to view
```
unzip -n ncbi_dataset.zip
cd ncbi_dataset
cd data
ls
cd GCF_000848505.1/
```

# Count each feature type in the GFF file
```
grep -v "^#" genomic.gff | cut -f3 | sort | uniq -c
```

## Number of features:
```
     11 CDS
      7 exon
      1 five_prime_UTR
      7 gene
      7 mRNA
      8 polyA_signal_sequence
      1 region
      8 regulatory_region
      4 sequence_feature
      1 three_prime_UTR
```


## Visual Insepction using IGV
### How big is the genome?
#### - Over 18kb

### What is the longest gene? What is its name and function?
#### - Longest gene/ locus tag = L / ZEBOVgp7
#### - function = codes for RNA-dependent RNA polymerase


### Look at another gene: give name and function
#### - gene name/locus tag = NP / ZEBOVgp1
#### - gene function = codes for nucleoprotein

### Using IGV estimate how much of the genome is covered by coding sequences? Are the genes closely packed? Is there a lot of intragenomic space? 
#### - It is very closely packed, with about 80-90% of the genome covered by coding sequences 



#### different genome to compare from human isolate = https://www.ncbi.nlm.nih.gov/nuccore/KU182898.1 
