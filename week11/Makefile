# Makefile for Ebola genome alignment project

# ----------------- Variable Definitions -----------------
ACC ?= AF086833
NAME ?= ebola-1976
SRR ?= SRR1972966 SRR1972967 SRR1972973
SPOTS ?= 1000
THREADS ?= 4

BAM_DIR := bam
BG := coverage/${SRR}.bedgraph
BIGWIG := coverage/${SRR}.bw
COVERAGE := coverage/${SRR}.txt
CHROMSIZES := refs/${NAME}.chrom.sizes
FAI := ${REF}.fai

REF := refs/${NAME}.fa
R1 := reads/${SRR}_1.fastq
R2 := reads/${SRR}_2.fastq
BAM := ${BAM_DIR}/${SRR}.bam

# ----------------- Shell Settings -----------------
SHELL := bash
.ONESHELL:
.SHELLFLAGS := -eu -o pipefail -c
.DELETE_ON_ERROR:
MAKEFLAGS += --warn-undefined-variables
MAKEFLAGS += --no-builtin-rules

# ----------------- Phony Targets -----------------
.PHONY: all usage refs fastq index align stats coverage clean alignment_summary bigwig variants 

# ----------------- Default Workflow -----------------
all: fastq fastqc align stats variants

# ----------------- Usage Message -----------------
usage:
	@echo "Usage: make [all|refs|fastq|index|align|stats|coverage|bigwig|clean] SRR=<SRR> NAME=<name> ACC=<acc>"

# ----------------- Download Reference Genome -----------------
refs:
	mkdir -p $(dir ${REF})
	bio fetch ${ACC} --format fasta > ${REF}
	bio fetch ${ACC} --format gff > refs/ebola_mayinga_1976.gff
	# create FASTA index and chrom.sizes needed for bigWig
	samtools faidx ${REF}
	cut -f1,2 ${REF}.fai > ${CHROMSIZES}
	seqkit stats ${REF}

# ----------------- Download FASTQ Reads -----------------
fastq:
	mkdir -p $(dir ${R1})
	# download reads (handles single-end and paired-end SRA runs)
	fastq-dump -X ${SPOTS} --split-files -O reads ${SRR}
	@if [ -f "${R2}" ]; then \
		seqkit stats ${R1} ${R2}; \
	else \
		seqkit stats ${R1}; \
	fi

fastqc:
	mkdir -p fastqc_reports
	fastqc reads/${SRR}_*.fastq -o fastqc_reports

# ----------------- Index Reference Genome -----------------
${REF}:
	make refs

${REF}.bwt: ${REF}
	bwa index ${REF}
index: ${REF}.bwt

# ----------------- align -> BAM + index -----------------
align: fastq ${REF}.bwt
	mkdir -p ${BAM_DIR}
	# align, sort, write BAM (handles single-end or paired-end)
ifneq ("$(wildcard ${R2})","")
	@echo "Detected paired-end reads: ${R1} ${R2}"
	@bwa mem -t ${THREADS} ${REF} ${R1} ${R2} | samtools sort -@ ${THREADS} -T tmp_prefix -o ${BAM}
else
	@echo "Detected single-end reads: ${R1}"
	@bwa mem -t ${THREADS} ${REF} ${R1} | samtools sort -@ ${THREADS} -T tmp_prefix -o ${BAM}
endif
	# index BAM
	samtools index ${BAM}

# ----------------- Simple Alignment Stats -----------------
stats: ${BAM}
	@echo "samtools flagstat for ${BAM}:"
	samtools flagstat ${BAM}
	@echo ""
	@echo "samtools coverage for ${BAM}:"
	samtools coverage ${BAM}

# ----------------- Alignment Summary -----------------
alignment_summary: ${BAM}
	@echo "Generating alignment summary for ${BAM}:"
	@total_reads=$$(samtools view -c -F 0x904 ${BAM}); \
	aligned_reads=$$(samtools view -c -F 0x4 ${BAM}); \
	perc=$$(awk -v a=$$aligned_reads -v t=$$total_reads 'BEGIN{ if (t>0) printf("%.2f", (a/t)*100) else print "NA" }'); \
	echo "Total primary alignments (excl unmapped/secondary/supplementary): $$total_reads"; \
	echo "Aligned reads (not flagged as unmapped): $$aligned_reads"; \
	echo "Percent aligned: $$perc %"; \
	\
	@echo ""; \
	@echo "Expected coverage estimate (approx):"; \
	@echo "Genome size (bp): $$(awk '{s+=$$2} END{print s}' ${CHROMSIZES})"; \
	@echo "Spots downloaded: ${SPOTS}"; \
	@echo "Estimated read length (bp): 200"; \
	@echo "Estimated total sequenced bases: "$$((${SPOTS} * 200)); \
	@echo "Estimated coverage (x): "$$((${SPOTS} * 200) / $$(awk '{s+=$$2} END{print s}' ${CHROMSIZES}))

# ----------------- bigWig Creation -----------------
# requires bedtools and bedGraphToBigWig (UCSC)
bigwig: align
	mkdir -p bigwig coverage
	# compute genome-wide bedGraph (depth per position)
	bedtools genomecov -ibam ${BAM} -bg > ${BG}
	# sort bedGraph by chrom/pos (bedGraphToBigWig expects sorted)
	sort -k1,1 -k2,2n ${BG} > ${BG}.sorted
	# ensure chromosome sizes exist (should be created by refs rule)
	@if [ ! -s ${CHROMSIZES} ]; then \
		if [ -s ${FAI} ]; then cut -f1,2 ${FAI} > ${CHROMSIZES}; else echo "Error: missing ${CHROMSIZES} and ${FAI}, run 'make refs' first" >&2; exit 1; fi \
	fi
	# convert to bigWig
	bedGraphToBigWig ${BG}.sorted ${CHROMSIZES} ${BIGWIG}
	@echo "Created ${BIGWIG}"

# ----------------- Coverage / Depth -----------------
$(COVERAGE): ${BAM}
	mkdir -p coverage
	samtools depth ${BAM} > ${COVERAGE}

coverage: ${COVERAGE}
	@echo "Summary stats from ${COVERAGE}:"
	@awk 'BEGIN {total=0; n=0; max=0; min=1e9} {total+=$$3; n++; if($$3>max) max=$$3; if($$3<min) min=$$3} END {if(n>0) printf "Avg coverage: %.2f\nMax: %d\nMin: %d\nPositions: %d\n", total/n, max, min, n; else print "No coverage positions";}' ${COVERAGE}
	@echo "Top 5 highest coverage positions:"
	@sort -k3,3nr ${COVERAGE} | head -n5

# ----------------- Clean Workspace -----------------
clean:
	rm -rf refs/* reads/* ${BAM_DIR}/* ${COVERAGE} coverage_plot.png bigwig coverage depth* $(BG)*


# ----------------- Variant Calling -----------------
variants:
	# make directory for vcf files
	mkdir -p VCF

	# Variant calling process
	bcftools mpileup -Ou -f ${REF} ${BAM} | \
	bcftools call -mv -Ou | \
	bcftools norm -f ${REF} -d all -Ou | \
	bcftools sort -Oz -o VCF/${SRR}.vcf.gz

	# index VCF
	bcftools index VCF/${SRR}.vcf.gz

# ----------------- Build & Annotate with SnpEff -----------------
LABEL = ebola_1976
SNEFF_REF = refs/ebola-1976.fa
SNEFF_GFF = refs/ebola_mayinga_1976.cleaned.gff
VCF_FILE = VCF/${SRR}.vcf.gz
ANN = VCF/${SRR}.annotated.vcf

# Clean GFF3 for SnpEff
refs/ebola_mayinga_1976.cleaned.gff: refs/ebola_mayinga_1976.gff
	awk -F'\t' 'NF>=9' $< | grep -v "misc_signal" | grep -v "five_prime_UTR" | grep -v "Note=" > $@

# Build SnpEff database
snpeff_build: refs/ebola_mayinga_1976.cleaned.gff
	mkdir -p data/${LABEL}
	cp ${SNEFF_REF} data/${LABEL}/sequences.fa
	cp ${SNEFF_GFF} data/${LABEL}/genes.gff
	touch snpEff.config
	@if ! grep -q "^${LABEL}.genome" snpEff.config; then \
		echo "${LABEL}.genome : Ebola Zaire Virus" >> snpEff.config; \
		echo "${LABEL}.reference : ebola_mayinga_1976" >> snpEff.config; \
		echo "${LABEL}.chromosomes : ebola_mayinga_1976" >> snpEff.config; \
	fi
	# build the database (note the tab at the start!)
	snpEff build -gff3 -v -noCheckCds -noCheckProtein ${LABEL}

# Annotate VCF using SnpEff with per-SRR summary
snpeff_run: snpeff_build
	mkdir -p VCF
	# generate a per-SRR summary HTML
	snpEff ann -v -stats VCF/${SRR}.snpEff_summary.html ${LABEL} ${VCF_FILE} > ${ANN}

# List of SRR IDs
SRR_LIST = SRR1972966 SRR1972967 SRR1972973

# target: snpeff_all
snpeff_all:
	for srr in SRR1972966 SRR1972967 SRR1972973; do \
		echo "Running SnpEff on $$srr"; \
		snpEff -c snpEff.config -v -noDownload ebola_1976 \
			VCF/$$srr.vcf.gz \
			-stats VCF/$$srr.snpEff_summary.html \
			> VCF/$$srr.annotated.vcf; \
	done

