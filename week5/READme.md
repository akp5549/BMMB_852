### 1. Identify BioPorject and SRR#
#### - SRA BioProject = PRJNA257197
#### - SRR number = SRR1735120
#### - URL = https://www.ncbi.nlm.nih.gov/sra/SRX824283[accn]


### 2. 10x Coverage calculation
#### - The size of the genome is about ~19kb
#### Target bases: 19,000 × 10 = 190,000 bases 
#### Illumina 2×100 bp ≈ ~200 bases/spot → 190,000/200 ≈ 950 spots ; to be safe I rounded up to 1000 spots

### 3. Expand on last weeks code and run a bash shell script & generate stats on read
#### Bash shell script =
```
#!/bin/bash

# week5.sh - Download ~10x coverage of Ebola virus genome and run FastQC

# -------------------- Configuration --------------------

SRR="SRR1735120"
SPOTS=1000                      # Approximate number of spots for 10x coverage
READS_DIR="reads"
REPORTS_DIR="fastqc_reports"

# Expected output filenames from fastq-dump
R1="${READS_DIR}/${SRR}_1.fastq"
R2="${READS_DIR}/${SRR}_2.fastq"

# -------------------- Create Directories --------------------

mkdir -p "$READS_DIR"
mkdir -p "$REPORTS_DIR"

# -------------------- Download Subset of Reads --------------------

echo "Downloading $SPOTS spots (~10x genome coverage for Ebola virus)..."
fastq-dump -X "$SPOTS" --split-files -O "$READS_DIR" "$SRR"

# -------------------- Run FastQC --------------------

echo "Running FastQC on downloaded reads..."
fastqc -o "$REPORTS_DIR" "$R1" "$R2"

# -------------------- Basic Read Statistics --------------------

echo "Generating basic statistics..."

awk '(NR%4==2){bases+=length($1); reads++} END{print FILENAME, "- Reads:", reads, "Total Bases:", bases, "Avg Read Length:", bases/reads}' "$R1"
awk '(NR%4==2){bases+=length($1); reads++} END{print FILENAME, "- Reads:", reads, "Total Bases:", bases, "Avg Read Length:", bases/reads}' "$R2"

echo "Done. FastQC reports saved in: $REPORTS_DIR"

```



#### Run bash script =
```
chmod +x week5.sh
./week5.sh
```



### 4. Run FASTQ and evaluate report
#### Forward reads:
<img width="825" height="600" alt="image" src="https://github.com/user-attachments/assets/58273b96-316d-4d75-aeff-15adbd58340b" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/d7f89a3a-bb04-47d7-9a6d-7403315fd868" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/f3d9fea6-ab32-4be9-ad88-ec61f670e944" />

#### Reverse reads:
<img width="825" height="600" alt="image" src="https://github.com/user-attachments/assets/c6d86c7d-9d68-4748-a198-cb1d4a9e3414" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/a109c437-771f-410d-bf60-3986b1d53f8b" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/a9eebd9a-bdaa-45b6-83bd-a4812224dbc4" />

#### Based on this, we can conclude that the quality is good for this dataset. Although the GC content is a litte off and on the higher end, the Per sequence quality scores and Per base sequence quality are satisfactory. 



### 5. Comparing quality report of another dataset of same genome, but using a different platfortm 
#### - SRX7443463: Ebola virus, OXFORD_NANOPORE
#### - SRR = SRR10769763
#### - https://www.ncbi.nlm.nih.gov/sra/SRX7443463[accn] 
#### Repeat bash script with new SRR # = 
```
#!/bin/bash

# week5-coompare.sh - Download ~10x coverage of Ebola virus genome and run FastQC

# -------------------- Configuration --------------------

SRR="SRR10769763"
SPOTS=1000                      # Approximate number of spots for 10x coverage
READS_DIR="reads"
REPORTS_DIR="fastqc_reports"

# Expected output filenames from fastq-dump
R1="${READS_DIR}/${SRR}_1.fastq"
R2="${READS_DIR}/${SRR}_2.fastq"

# -------------------- Create Directories --------------------

mkdir -p "$READS_DIR"
mkdir -p "$REPORTS_DIR"

# -------------------- Download Subset of Reads --------------------

echo "Downloading $SPOTS spots (~10x genome coverage for Ebola virus)..."
fastq-dump -X "$SPOTS" --split-files -O "$READS_DIR" "$SRR"

# -------------------- Run FastQC --------------------

echo "Running FastQC on downloaded reads..."
fastqc -o "$REPORTS_DIR" "$R1" "$R2"

# -------------------- Basic Read Statistics --------------------

echo "Generating basic statistics..."

awk '(NR%4==2){bases+=length($1); reads++} END{print FILENAME, "- Reads:", reads, "Total Bases:", bases, "Avg Read Length:", bases/reads}' "$R1"
awk '(NR%4==2){bases+=length($1); reads++} END{print FILENAME, "- Reads:", reads, "Total Bases:", bases, "Avg Read Length:", bases/reads}' "$R2"

echo "Done. FastQC reports saved in: $REPORTS_DIR"
```

#### This is a single read set:
<img width="915" height="600" alt="image" src="https://github.com/user-attachments/assets/7b9dcb94-4c7c-4fb9-9d89-d79b3f7cf0c8" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/40b18c71-f1bd-4cc5-b88b-fcd3fd31fe41" />

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/ce4024c6-e796-405e-b7cf-3c32d371f294" />




#### Comparing them, aside from the fact that we know paired-end data is more robust than single-end data, we can clearly see the quality of FASTQ files is better in the Illumina sequence. 
