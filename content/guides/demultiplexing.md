+++
title = "Demultiplexing"
template = "page.html"
weight = 2
+++

Often, we have a reference file of known sequences such as barcodes, and we want to assign each read to its barcode.

## Barcode discovery

Before loading a reference file, it can be useful to quantify the barcodes that are actually present in the reads. This may be used to create a subset of the barcode reference file, as per the approach of <a href="https://davidsongroup.github.io/flexiplex/">flexiplex</a>. 

If the barcode flanks a known sequence:

```matchbox
primer = AGCTAGTCGATGC

# build a count matrix of all the raw sequences
# found at the expected barcode location
if read is [_ primer bc:|16| _] => bc.seq.count!()
```

## Searching for barcodes from a reference file

Typically, demultiplexing involves a list of known barcodes

If your barcodes are in FASTA format, the `fasta` function can be used. Then, to iterate over the barcodes, a pattern can be parameterised using `for`:

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read is [_ primer bc.seq _] for bc in bcs =>
    read.tag('barcode={bc.id}')
        .out!('demultiplexed.fq')
```

<!-- Above, each `bc` in the list of `bcs` is a FASTA <code class="type">Read</code>, with an `id`, `seq` and `desc` field. -->

Alternatively, CSV or TSV files can be used. The values returned by the `csv` and `tsv` functions contain fields as named in the CSV/TSV header. Hence, for the following CSV, the fields would be `barcode_name` and `sequence`.

```csv
barcode_name,sequence
bc1,AAAA
bc2,GGGG
```

```matchbox
primer = AGCTAGTCGATGC
bcs = csv('my_barcodes.csv')

if read is [_ primer bc.sequence _] for bc in bcs =>
    read.tag('barcode={bc.barcode_name}')
        .out!('demultiplexed.fq')
```

In the above examples, each read is tagged with the name of its barcode, and all successfully demultiplexed reads are aggregated into `demultiplexed.fq`. 


<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top; width:2em;text-align:center;padding-top:0.5em">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
            The <code>fasta</code>, <code>csv</code> and <code>tsv</code> functions load the entire reference file into memory at the start of execution. <i>matchbox</i> currently doesn't support reference files which are too large to load into memory!
        </td>
    </tr>
</table>
</div>

## Demultiplexing into separate files

It can be preferable to create separate files for each barcode:

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read is [_ primer bc.seq _] for bc in bcs =>
    read.out!('{bc.id}_reads.fq')
```

## Multiple barcodes from different barcode lists

Sometimes, multiple distinct barcodes are present in a read. 

If two barcodes come from separate barcode lists, two references can be loaded in. Multiple `for` parameters can be given, separated by commas: 

```matchbox
primer = AGCTAGTCGATGC
bcs1 = fasta('my_barcodes1.fa')
bcs2 = fasta('my_barcodes2.fa')

if read is [_ primer bc1.seq linker bc2.seq _] 
    for bc1 in bcs1, bc2 in bcs2 =>
        read.tag('barcode1={bc1.name}')
            .tag('barcode2={bc2.name}')
            .out!('demultiplexed.fq')
```

## Multiple barcodes from the same barcode list

If two barcodes are distinct barcodes from the same reference list, we can load the reference file once and then create two parameters over it:

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read is [_ primer bc1.seq linker bc2.seq _] 
    for bc1 in bcs, bc2 in bcs =>
        read.tag('barcode1={bc1.name}')
            .tag('barcode2={bc2.name}')
            .out!('demultiplexed.fq')
```

## Finding the same barcode in multiple places

If we expect to find the exact same barcode in two places within a read, we can use a single parameter and search for it in multiple places in the read pattern:

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read is [_ primer bc.seq linker bc.seq _] 
    for bc in bcs =>
        read.tag('barcode={bc.name}')
            .out!('demultiplexed.fq')
```

## Barcodes with multiple fields

Sometimes a single reference contains multiple parts, which must all match in different places in a read.

```csv
name,part1,part2
bc1,AAAA,GGGGAC
bc2,GGGG,ACGATG
```

```matchbox
refs = csv('my_reference.csv')

if read is [_ static1 ref.part1 static2 ref.part2 _]
    for ref in refs => read.out!('{ref.name}.fa')
```
