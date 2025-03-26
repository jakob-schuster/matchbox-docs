+++
title = "Quick start"
sort_by = "title"
template = "index.html"
+++

Welcome to the official user documentation for *matchbox*, a flexible preprocessor for FASTA/FASTQ/SAM/BAM files.

## Installation

To install *matchbox*, clone the GitHub repo and build it using cargo. More accessible distribution coming soon!

```bash
git clone https://github.com/jakob-schuster/matchbox.git
cd matchbox
cargo build --release

# then, run matchbox from the release directory
./target/release/matchbox --help
```

## Command-line usage

*matchbox* takes in reads, in FASTA, FASTQ, SAM or BAM formats. If no file path is given, *matchbox* expects to receive reads from stdin.

*matchbox* also requires a configuration script, written in the *matchbox* scripting language, as a `.mb` file. This script will tell *matchbox* how to process your reads.

```bash
./target/release/matchbox -s my_script.mb reads.fq
```

### Paired-end reads

If your reads are paired-end, the `--paired-with` parameter can be used.

```bash
./target/release/matchbox -s my_script.mb read1.fq.gz --paired-with read2.fq.gz
```

### Error tolerance

When performing pattern-matching, *matchbox* tolerates insertions, deletions and substitutions. The global error rate is used for all sequences. 

```bash
# 15% error rate
./target/release/matchbox -s my_script.mb reads.fq -e 0.15

# 0% error rate (only search for exact matches)
./target/release/matchbox -s my_script.mb reads.fq -e 0
```