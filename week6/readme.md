

## 1. Create a Makefile that addresses the following:
#### - Obtaining the genome and downloading sequencing reads from SRA
#### - Include the following targets: Index the genome and generate a sorted and indexed BAM file by aligning reads to the genome
```
# Makefile for Ebola genome alignment project

# Variables
ACC=AF086833
NAME=ebola-1976
SRR=SRR1735120
SPOTS=1000

REF=refs/${NAME}.fa
R1=reads/${SRR}_1.fastq
R2=reads/${SRR}_2.fastq
BAM=bam/${SRR}.bam

SHELL := bash
.ONESHELL:
.SHELLFLAGS := -eu -o pipefail -c
.DELETE_ON_ERROR:
MAKEFLAGS += --warn-undefined-variables
MAKEFLAGS += --no-builtin-rules

all: refs fastq index align stats

usage:
	@echo "Usage: make [all|refs|fastq|index|align|stats|coverage|clean]"

refs:
	mkdir -p $(dir ${REF})
	bio fetch ${ACC} --format fasta > ${REF}
	seqkit stats ${REF}

fastq:
	mkdir -p $(dir ${R1})
	fastq-dump -X ${SPOTS} --split-files -O reads ${SRR}
	seqkit stats ${R1} ${R2}

index:
	bwa index ${REF}

align:
	mkdir -p $(dir ${BAM})
	bwa mem -t 4 ${REF} ${R1} ${R2} | samtools sort -o ${BAM}
	samtools index ${BAM}

stats:
	samtools flagstat ${BAM}
	samtools coverage ${BAM}

coverage.txt: ${BAM}
	samtools depth ${BAM} > coverage.txt

coverage: coverage.txt
	Rscript plot_coverage.R

clean:
	rm -rf refs/* reads/* bam/* coverage.txt coverage_plot.png

# Calculate depth file from BAM
coverage.txt: ${BAM}
	samtools depth ${BAM} > coverage.txt

# Visualize and summarize coverage
coverage: coverage.txt
	@echo "Generating coverage statistics..."
	@awk 'BEGIN {total=0; n=0; max=0; min=999999} \
	     {total+=$3; n++; if ($3>max) max=$3; if ($3<min) min=$3} \
	     END {print "Observed Average Coverage:", total/n; print "Max Coverage:", max; print "Min Coverage:", min}' coverage.txt

	@echo ""
	@echo "Simple visual estimate of coverage variation (binned ASCII bars):"
	@awk '{bin=int($3/5); count[bin]++} END {for (b in count) {printf "%3d-%-3d: ", b*5, b*5+4; for(i=0;i<count[b];i++) printf "█"; print ""}}' coverage.txt | sort -n

# Get percent aligned and expected coverage
alignment_summary: ${BAM}
	@echo "Generating alignment summary..."
	@total_reads=$$(samtools view -c -f 1 ${BAM}); \
	aligned_reads=$$(samtools view -c -F 4 ${BAM}); \
	perc=$$(awk -v a=$$aligned_reads -v t=$$total_reads 'BEGIN { printf("%.2f", (a/t)*100) }'); \
	echo "Total Reads: $$total_reads"; \
	echo "Aligned Reads: $$aligned_reads"; \
	echo "% Aligned: $$perc%"

	@echo ""
	@echo "Expected Coverage Calculation (~10x genome):"
	@echo "Genome size: 19,000 bp"
	@echo "Spots downloaded: ${SPOTS}"
	@echo "Read length ~200 bp/spot"
	@echo "Expected bases = ${SPOTS} * 200 = "$$((${SPOTS} * 200))" bases"
	@echo "Expected coverage = total bases / genome size = "$$((${SPOTS} * 200 / 19000))"x"

```


## 2. explain the use of the Makefile in your project
### - We use a Makefile for this project 


## 3. Visualize the resulting BAM files for both simulated reads and reads downloaded from SRA.

<img width="1918" height="198" alt="image" src="https://github.com/user-attachments/assets/1b321876-d516-41e2-ad6a-c80ea443a0bc" />



## 4. Generate alignment statistics for the BAM file.
### A. What percentage of reads aligned to the genome?
### - ~ 0%
### B. What was the expected average coverage?
### - ~ 5.3×
### C. What is the observed average coverage?
### - observed average coverage = 0×
### D. How much does the coverage vary across the genome? (Provide a visual estimate.)
### - Very high / uneven: Coverage is 0 across the entire genome

