# Makefile for cancer genome alignment project (KRAS region)

# ----------------- Variable Definitions -----------------
ACC ?= GCA_000001405.28
NAME ?= PRJNA200694
SRR ?= SRR11321730 SRR30646122 SRR30646126
SPOTS ?= 1000
THREADS ?= 4

# KRAS region coordinates
CHR := chr12
START := 25205246
END := 25250936

# Reference for KRAS only
FULL_REF := refs/chr12.fasta      # full chromosome from step 1
REF := refs/KRAS.fasta            # extracted KRAS region
CHROMSIZES := refs/KRAS.chrom.sizes
FAI := ${REF}.fai

# Per-sample derived filenames
R1 = reads/$1_1.fastq
R2 = reads/$1_2.fastq
BAM = bam/$1.bam

# ----------------- Shell Settings -----------------
SHELL := bash
.ONESHELL:
.SHELLFLAGS := -eu -o pipefail -c
.DELETE_ON_ERROR:
MAKEFLAGS += --warn-undefined-variables
MAKEFLAGS += --no-builtin-rules

# ----------------- Phony Targets -----------------
.PHONY: all usage refs fastq fastqc index align stats clean alignment_summary extract_region

# ----------------- Default Workflow -----------------
all: extract_region fastq fastqc align stats

# ----------------- Usage Message -----------------
usage:
	@echo "Usage: make [all|refs|fastq|index|align|stats] SRR=<run> NAME=<name> ACC=<acc>"

# ----------------- Extract KRAS Region -----------------
extract_region:
	mkdir -p refs
	if [ ! -s ${REF} ]; then \
		echo "Extracting KRAS region from ${FULL_REF}"; \
		samtools faidx ${FULL_REF} ${CHR}:${START}-${END} > ${REF}; \
		samtools faidx ${REF}; \
		cut -f1,2 ${REF}.fai > ${CHROMSIZES}; \
	else \
		echo "KRAS reference already exists at ${REF}"; \
	fi

# ----------------- Reference Genome (skip download) -----------------
refs:
	@echo "Reference genome assumed downloaded as ${FULL_REF} (from Step 1)"

# ----------------- Download FASTQ Reads -----------------
fastq:
	mkdir -p reads
	for s in ${SRR}; do \
		fastq-dump -X ${SPOTS} --split-files -O reads $$s; \
	done
	seqkit stats reads/*

fastqc:
	mkdir -p fastqc_reports
	fastqc reads/*.fastq -o fastqc_reports

# ----------------- Index Reference Genome -----------------
${REF}.bwt:
	bwa index ${REF}

index: ${REF}.bwt

# ----------------- Align each SRR separately -----------------
align: index fastq
	mkdir -p bam
	for s in ${SRR}; do \
		if [ -f "reads/$${s}_2.fastq" ]; then \
			echo "Paired-end for $$s"; \
			bwa mem -t ${THREADS} ${REF} reads/$${s}_1.fastq reads/$${s}_2.fastq \
			| samtools sort -@ ${THREADS} -o bam/$${s}.bam; \
		else \
			echo "Single-end for $$s"; \
			bwa mem -t ${THREADS} ${REF} reads/$${s}_1.fastq \
			| samtools sort -@ ${THREADS} -o bam/$${s}.bam; \
		fi; \
		samtools index bam/$${s}.bam; \
	done

# ----------------- Per-sample SAM/BAM Stats -----------------
stats:
	for s in ${SRR}; do \
		echo "===== $$s ====="; \
		samtools flagstat bam/$${s}.bam; \
		echo ""; \
		samtools coverage bam/$${s}.bam; \
		echo ""; \
	done

# ----------------- Alignment Summary -----------------
alignment_summary:
	for s in ${SRR}; do \
		echo "===== $$s ====="; \
		total_reads=$$(samtools view -c -F 0x904 bam/$${s}.bam); \
		aligned_reads=$$(samtools view -c -F 0x4 -F 0x904 bam/$${s}.bam); \
		perc=$$(awk -v a=$$aligned_reads -v t=$$total_reads 'BEGIN{ if(t>0) printf("%.2f", (a/t)*100); else print "NA" }'); \
		echo "Total primary reads: $$total_reads"; \
		echo "Aligned reads: $$aligned_reads"; \
		echo "Percent aligned: $$perc %"; \
		echo ""; \
	done

clean:
	rm -rf bam reads fastqc_reports refs/KRAS.fasta refs/KRAS.fasta.fai refs/KRAS.chrom.sizes
