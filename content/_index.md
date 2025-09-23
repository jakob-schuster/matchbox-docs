+++
title = "Quick start"
sort_by = "weight"
template = "index.html"
+++

Welcome to the official user documentation for *matchbox*, a flexible processor for FASTA/FASTQ/SAM/BAM files.

You could use *matchbox* for:
- Quick, error-tolerant searches for primers and known sequences
- Validating the structure of your reads
- Quantifying and filtering out sequencing artefacts
- Demultiplexing even the most complex barcoding schemes

<!-- 
[Recipes](/recipes/) contains plenty of example scripts to get started with *matchbox*.

[Reference](/reference/) is an in-depth reference for the *matchbox* scripting language, including a list of the built-in functions. -->

## Installation

Clone the GitHub repo and build it using cargo. More accessible distribution coming soon!

```bash
git clone https://github.com/jakob-schuster/matchbox.git
cd matchbox
cargo build --release

# then, run matchbox from the release directory
./target/release/matchbox --help
```

## Command-line usage

*matchbox* takes in reads, in FASTA, FASTQ, SAM or BAM formats. If no file path is given, *matchbox* expects to receive reads from stdin.

*matchbox* also requires a configuration script, written in the *matchbox* scripting language as a `.mb` file. This script will tell *matchbox* how to process your reads.

```bash
matchbox -s my_script.mb reads.fq
```

### Paired-end reads

If your reads are paired-end, the `--paired-with` parameter can be used.

```bash
matchbox -s my_script.mb read1.fq.gz --paired-with read2.fq.gz
```

### Error tolerance

When performing pattern-matching, *matchbox* tolerates insertions, deletions and substitutions. The global error rate is used for all sequences.

```bash
# 15% error rate
matchbox -s my_script.mb reads.fq -e 0.15

# 0% error rate (only search for exact matches)
matchbox -s my_script.mb reads.fq -e 0
```

<!-- ### Scripting language


<table>
<tr>
<td>Quick, simple queries</td>
<td>

```matchbox
read.seq.len().average!()
```

</td>
</tr>
<tr>
<td>Powerful pattern matching</td>
<td>

```matchbox
if read matches [_ primer umi:|10| _] =>
    read.tag('umi={umi.seq}')
        .out!('processed.fq')
```

</td>
</tr>
<tr>
<td>Complex demultiplexing</td>
<td>

```matchbox
if read matches [_ b1.seq linker b2.seq _] 
    for b1, b2 in csv('barcodes.tsv') =>
        read.out!('{b1.seq}_{b2.seq}.fq')
```

</td>
</tr>
</table> -->
