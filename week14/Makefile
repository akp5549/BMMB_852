# ----------------- Variable Definitions -----------------
ACC ?= NC_007793.1
GCF ?= GCF_000013465.1
SRR ?=
NAME ?= staphylococcus_aureus
THREADS ?= 8
MAX_TRIES ?= 2
SPOTS ?= 1000000  
DESIGN := design.csv


BAM_DIR := bam
BG := coverage/${SRR}.bedgraph
BIGWIG := coverage/${SRR}.bw
COVERAGE := coverage/${SRR}.txt

REF := refs/${GCF}.fa
GFF := refs/${GCF}.gff
CHROMSIZES := refs/${GCF}.chrom.sizes
FAI := ${REF}.fai

R1 := reads/${SRR}_1.fastq
R2 := reads/${SRR}_2.fastq
BAM := ${BAM_DIR}/${SRR}.bam

# ----------------- All Samples -----------------
ALL_SRRS := $(shell tail -n +2 ${DESIGN} | cut -d',' -f1)
ALL_BAMS := $(patsubst %,${BAM_DIR}/%.bam,${ALL_SRRS})

# Pattern rule to make BAMs for a single SRR
${BAM_DIR}/%.bam:
	$(MAKE) SRR=$* all

# Build all BAMs first, then generate counts
all_samples: ${ALL_BAMS}
	@echo "All BAMs exist. Now generating count matrix..."
	$(MAKE) counts NAME=${NAME} THREADS=${THREADS}
	$(MAKE) rna_seq


# ----------------- Shell Settings -----------------
SHELL := bash
.ONESHELL:
.SHELLFLAGS := -eu -o pipefail -c
.DELETE_ON_ERROR:
MAKEFLAGS += --warn-undefined-variables
MAKEFLAGS += --no-builtin-rules

# ----------------- Phony Targets -----------------
.PHONY: all usage refs fastq index align stats coverage clean alignment_summary bigwig all_samples counts rna_seq pca heatmap de gprofiler enrichr

# ----------------- Default Workflow -----------------
all: fastq fastqc align stats coverage bigwig

# ----------------- Usage Message -----------------
usage:
	@echo "Usage: make [all|refs|fastq|index|align|stats|coverage|bigwig|clean] SRR=<SRR> NAME=<name> ACC=<acc>"

# ----------------- Download Reference Genome & Annotations -----------------
refs: ${REF} ${GFF} ${FAI} ${CHROMSIZES}

# Fetch FASTA
${REF}:
	mkdir -p refs
	@echo "Fetching reference genome ${GCF}..."
	bio fetch ${GCF} --format fasta > ${REF}

# Fetch GFF
${GFF}:
	mkdir -p refs
	@echo "Fetching annotation for ${GCF}..."
	bio fetch ${GCF} --format gff > ${GFF}

# Index FASTA
${FAI}: ${REF}
	@echo "Indexing reference genome..."
	samtools faidx ${REF}

# Generate chrom.sizes
${CHROMSIZES}: ${FAI}
	@echo "Generating chromosome sizes..."
	cut -f1,2 ${FAI} > ${CHROMSIZES}

# Index for BWA
${REF}.bwt: ${REF}
	bwa index ${REF}

index: ${REF}.bwt


# ----------------- Download FASTQ Reads -----------------
fastq:
	mkdir -p $(dir ${R1})
	if [ -s "${R1}" ] && { [ ! -f "${R2}" ] || [ -s "${R2}" ]; }; then \
		echo "FASTQ files for ${SRR} already exist, skipping download"; \
	else \
		MAX_TRIES=3; i=0; \
		until [ $$i -ge $$MAX_TRIES ]; do \
			echo "Downloading ${SRR}, attempt $$((i+1))/${MAX_TRIES}..."; \
			fastq-dump -X ${SPOTS} --split-files -O reads ${SRR} && break; \
			i=$$((i+1)); \
			echo "fastq-dump failed, retrying in 5 seconds..."; \
			sleep 5; \
		done; \
		if [ $$i -ge $$MAX_TRIES ]; then \
			echo "Error: failed to download ${SRR} after $$MAX_TRIES attempts"; \
			exit 1; \
		fi \
	fi
	@if [ -f "${R2}" ]; then \
		seqkit stats ${R1} ${R2}; \
	else \
		seqkit stats ${R1}; \
	fi

fastqc:
	mkdir -p fastqc_reports
	fastqc reads/${SRR}_*.fastq -o fastqc_reports

# ----------------- Index Reference Genome -----------------

${REF}.bwt: ${REF}
	bwa index ${REF}
index: ${REF}.bwt

# ----------------- align -> BAM + index -----------------
# align -> BAM + index
align: fastq ${REF}.bwt refs
	mkdir -p ${BAM_DIR}
	if [ -s "${R2}" ]; then \
		echo "Detected paired-end reads: ${R1} ${R2}"; \
		bwa mem -t ${THREADS} ${REF} ${R1} ${R2} | samtools sort -@ ${THREADS} -T tmp_prefix -o ${BAM}; \
	else \
		echo "Detected single-end reads: ${R1}"; \
		bwa mem -t ${THREADS} ${REF} ${R1} | samtools sort -@ ${THREADS} -T tmp_prefix -o ${BAM}; \
	fi
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
	@echo "Generating coverage for ${BAM}..."
	-@samtools depth ${BAM} > ${COVERAGE} || true

coverage: ${COVERAGE}
	@echo "Summary stats from ${COVERAGE}:"
	-@if [ -s ${COVERAGE} ]; then \
		awk 'BEGIN {total=0; n=0; max=0; min=1e9} {total+=$$3; n++; if($$3>max) max=$$3; if($$3<min) min=$$3} END {if(n>0) printf "Avg coverage: %.2f\nMax: %d\nMin: %d\nPositions: %d\n", total/n, max, min, n; else print "No coverage positions";}' ${COVERAGE}; \
		echo "Top 5 highest coverage positions:"; \
		sort -k3,3nr ${COVERAGE} | head -n5 || true; \
	else \
		echo "Warning: ${COVERAGE} is empty, skipping stats"; \
	fi


# ----------------- Count Matrix Generation -----------------
counts: ${ALL_BAMS} ${GFF}
	mkdir -p res
	featureCounts \
  	-a ${GFF} \
 	 -F GFF \
 	 -t CDS \
 	 -g locus_tag \
 	 -p \
 	 -B \
 	 -C \
 	 -T ${THREADS} \
 	 -o res/all_counts.txt \
 	 ${ALL_BAMS}

	# Convert to CSV with gene IDs and SRR/sample names
	rm -f res/counts.csv
	awk 'NR==1{gsub("\t",","); print; next} NR>2{gsub("\t",","); print}' res/all_counts.txt > res/counts.csv

# ----------------- RNA-Seq Analysis -----------------
RNA_DIR := rna_seq
PCA_PDF := ${RNA_DIR}/pca.pdf
HEATMAP_PDF := ${RNA_DIR}/heatmap.pdf
DE_RESULTS := ${RNA_DIR}/edger.csv
GPROFILER_CSV := ${RNA_DIR}/gprofiler.csv
ENRICHR_CSV := ${RNA_DIR}/enrichr.csv

# Ensure RNA-Seq output folder exists
${RNA_DIR}:
	mkdir -p ${RNA_DIR}

# ----------------- PCA Plot -----------------
pca: counts.csv ${RNA_DIR}
	@echo "Generating PCA plot..."
	Rscript src/r/plot_pca.r -c counts.csv -d design.csv -o ${PCA_PDF}

# ----------------- Heatmap -----------------
heatmap: counts.csv ${RNA_DIR}
	@echo "Generating heatmap..."
	Rscript src/r/plot_heatmap.r -c counts.csv -d design.csv -o ${HEATMAP_PDF}

# ----------------- Differential Expression -----------------
de: counts.csv ${RNA_DIR}
	@echo "Running differential expression analysis..."
	Rscript src/r/edger.r -c counts.csv -d design.csv -o ${DE_RESULTS}

# ----------------- Functional Enrichment -----------------
gprofiler: ${DE_RESULTS} ${RNA_DIR}
	@echo "Running g:Profiler enrichment..."
	bio gprofiler -c ${DE_RESULTS} -o ${GPROFILER_CSV}

enrichr: ${DE_RESULTS} ${RNA_DIR}
	@echo "Running Enrichr enrichment..."
	bio enrichr -c ${DE_RESULTS} -o ${ENRICHR_CSV}

# ----------------- Combined RNA-Seq Workflow -----------------
rna_seq: de pca heatmap gprofiler enrichr
	@echo "RNA-Seq analysis completed. Outputs in ${RNA_DIR}"


# ----------------- Clean Workspace -----------------
clean:
	rm -rf refs/* reads/* ${BAM_DIR}/* ${COVERAGE} coverage_plot.png bigwig coverage depth* $(BG)*
