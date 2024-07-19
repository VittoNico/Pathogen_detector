# Viral_detection
Viral Contig Assembly Pipeline

This repository offers a comprehensive pipeline for assembling viral contigs from sequencing data. It combines several bioinformatics tools to perform quality checks, read trimming, removal of host genome sequences, contig assembly, and BLAST search against a custom database. Each script and command provided in this repository is tailored for high-throughput genomic analysis, ensuring accuracy and efficiency in processing large datasets.
Quality Check and Trimming 

This step involves quality checking and trimming of raw reads using fastp, a versatile tool for fast and reliable preprocessing of sequencing data

Removing Genome from Cleaned Reads

The cleaned reads are then mapped to the host genome to remove any sequences originating from the host. Bowtie is used for this purpose, and unmapped reads are retained for further analysis.

# Building Viral Contigs 

The unmapped reads are then used to assemble contigs using SPAdes, specifically the metagenomic mode metaSPAdes, which is optimized for complex datasets.

# Download and Prepare BLAST Database

A custom BLAST database is created using sequences from bacterial genomes. This involves downloading the required scripts and data, and constructing the database.

BLAST Search Against SPAdes Contigs

The assembled contigs are then searched against the custom bacterial database using BLAST, to identify and characterize the viral sequences.

This pipeline ensures that the final output consists of high-quality viral contigs, free from host genome contamination, and annotated against a relevant database for further analysis and interpretation.
