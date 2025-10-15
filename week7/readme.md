# Using the same MakeFile as last week, I added changes and updated the code


# The 2 SRRs I compared:
```
make all SRR=SRR35257019 NAME=ebola-1976 ACC=AF086833 
make all SRR=SRR1972739 NAME=ebola-1976 ACC=AF086833 
```

# 1. Briefly describe the differences between the alignment in both files.
### - SRR35257019 had a very high mapping rate (~99.65% primary mapped) and nearly all reads were properly paired (1986/2000).
### - SRR1972739 had a lower mapping rate (~73.80% primary mapped) and fewer properly paired reads (1466/2000).
### - This means  SRR35257019 aligns better to the reference genome, suggesting higher data quality or less divergence

# 2.Briefly compare the statistics for the two BAM files.
| Statistic             | SRR35257019 | SRR1972739 |
| --------------------- | ----------- | ---------- |
| Total reads           | 2000        | 2000       |
| Primary mapped reads  | 1993        | 1476       |
| Properly paired reads | 1986        | 1466       |
| Coverage (%)          | 98.36%      | 97.82%     |
| Mean depth            | 15.49       | 7.75       |

### - SRR35257019 has higher mapping efficiency, depth, and properly paired reads.
### - SRR1972739 has lower depth and more unmapped or improperly paired reads.

# 3. How many primary alignments does each of your BAM files contain?
### - SRR35257019: 1993 primary alignments
### - SRR1972739: 1476 primary alignments

# 4. What coordinate has the largest observed coverage?
### - SRR35257019: position 6083 with 81 reads
### - SRR1972739: position 17435 with 42 reads

# 5. Select a gene of interest. How many alignments on the forward strand cover the gene?
### - Gene chosen: VP24 (positions 5678–6433)
```
for S in SRR35257019 SRR1972739; do
  BAM=bam/$S.bam
  REGION="AF086833.2:5678-6433"   # VP24 coordinates
  forward=$(samtools view -c -F 16 "$BAM" "$REGION")
  echo -e "$S\tForward_reads_over_VP24\t$forward"
done
```
### - Alignments covering VP24: 
### SRR35257019 → 26 and SRR1972739 → 47
