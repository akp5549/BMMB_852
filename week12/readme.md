## 1. Get the reference genome
```
# Set the URL of the reference genome
REF_URL=https://ftp-trace.ncbi.nlm.nih.gov/ReferenceSamples/giab/release/references/GRCh38/GRCh38_GIABv3_no_alt_analysis_set_maskedGRC_decoys_MAP2K3_KMT2C_KCNJ18.fasta.gz

# Download the reference genome
REF=refs/GRCh38.fasta.gz

# The chromosome of interest.
CHR=chr12

# A subset of the reference genome for the region of interest.
REF=refs/${CHR}.fasta

# Create the reference directory
mkdir -p refs

# Download the reference genome
curl -L ${REF_URL} > ${REF}

# Index the reference genome for use with samtools.
samtools faidx ${REF}
```

## 2. Get alignments
```
# Set the URL of the BAM file
BAM_URL=https://ftp-trace.ncbi.nlm.nih.gov/ReferenceSamples/giab/data_somatic/HG008/Liss_lab/Element-AVITI-20241216/HG008-N-D_Element-StdInsert_77x_GRCh38-GIABv3.bam

# The BAM file name.
BAM=bam/KRAS-N-P.bam

# Set the region of interest.
REGION=chr12:25,205,246-25,250,936

# Create the BAM directory
mkdir -p bam

# Extract the reads for the region of interest.
samtools view -b ${BAM_URL} ${REGION} > ${BAM}

# Index the BAM file.
samtools index ${BAM}

# Get statistics on the BAM file.
samtools flagstat ${BAM}
```

## 3. Unzip full genome file
```
gunzip -c refs/GRCh38.fasta.gz > refs/GRCh38.fasta
samtools faidx refs/GRCh38.fasta

```

## 4. Use GNU parallel to run makefile on all samples:
```
cat design.csv | \
    parallel --colsep , --header : --eta --lb -j 1 \
        make SRR={Run} NAME=ebola-1976 SAMPLE={Sample} GROUP={Group} CONDITION={Condition} all
```

## 5. Comparing alignment statistics:


## 6. IGV
