# Viral_detection
This repository offers a comprehensive pipeline for assembling viral contigs from sequencing data. It combines several bioinformatics tools to perform quality checks, read trimming, removal of host genome sequences, contig assembly, and BLAST search against a custom database. Each script and command provided in this repository is tailored for high-throughput genomic analysis, ensuring accuracy and efficiency in processing large datasets.
# Quality Check and Trimming 
This step involves quality checking and trimming of raw reads using fastp, a versatile tool for fast and reliable preprocessing of sequencing data
```
fastp -i reads_1.fq.gz -I reads_2.fq.gz -o 1-fastp_data/OL_Rap_1_preprocessed.fastq.gz -O OL_Rap_2_preprocessed.fastq.gz -h 1-fastp_data/OL_Rap.html -j OL_Rap.json --qualified_quality_phred 20 --unqualified_percent_limit 30 --average_qual 25 --low_complexity_filter --complexity_threshold 30 --thread 15 --length_required 60 --overrepresentation_analysis --detect_adapter_for_pe
```

# Removing Genome from Cleaned Reads
The cleaned reads are then mapped to the host genome to remove any sequences originating from the host. Bowtie is used for this purpose, and unmapped reads are retained for further analysis.
```
bowtie-build -f thaliana.fna genome
bowtie -p 30 genome -1 reads_clean_1.fq.gz -2 reads_clean_2.fq.gz -S genome.sam --un-conc unmapped --chunkmbs 200
```

# Building Viral Contigs 

The unmapped reads are then used to assemble contigs using SPAdes, specifically the metagenomic mode metaSPAdes, which is optimized for complex datasets. The choice of the kmers can change from which organism you are searching (virus,fungi,bacteria...)
```
metaspades.py -t 30 -m 200 -1 unmapped.1.fq -2 unmapped.2.fq -k 97,107,117,127 -o spades_result
```

# Download and Prepare BLAST Database

A custom BLAST database is created using sequences from bacterial genomes. This involves downloading the required scripts and data, and constructing the database.
```
wget ftp://ftp.ncbi.nlm.nih.gov/blast/db/update_blastdb.pl
./update_blastdb.pl --decompress nt
esearch -db nuccore -query "txid2[Organism] NOT wgs[prop]" | efetch -format acc > bacterial_accessions.txt
blastdbcmd -db nt -entry_batch bacterial_accessions.txt -out bacteria.fasta
makeblastdb -in bacteria.fasta -dbtype nucl -title bacteria
```

#BLAST Search Against SPAdes Contigs

The assembled contigs are then searched against the custom bacterial database using BLAST, to identify and characterize the viral sequences.
```
blastn -db bacteria.fasta -num_threads 28 -task blastn -query spades_result/contigs.fasta -out result_2.xml -max_hsps 1 -max_target_seqs 5 -evalue 1e-3 -outfmt 5
```
This pipeline ensures that the final output consists of high-quality viral contigs, free from host genome contamination, and annotated against a relevant database for further analysis and interpretation.
