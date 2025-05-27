+++
title = "Function list"
template = "page.html"
weight = 3
+++

Functions are used to manipulate and transform data. Most of the time, users will be applying built-in *matchbox* functions. This list includes every built-in function with examples.


<br>
<br>

---

<div class="function_block">

## `len: `<code class="type">Num</code>

Calculates the length of a string.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to calculate the length of.</td>
</tr>
</table>

```matchbox
# get the average read length
read.seq.len().average!()
```

</div>

<br>
<br>

---

<div class="function_block">

## `slice: `<code class="type">Str</code>

Takes a slice of a string. Inclusive of start position, exclusive of end position.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to slice.</td>
</tr>
<tr>
<td><code>start: </code><code class="type">Num</code></td><td>The start position.</td>
</tr>
</tr>
<tr>
<td><code>end: </code><code class="type">Num</code></td><td>The end position.</td>
</tr>
</table>

```matchbox
# equal to 'ell'
sliced_string = slice('hello', 1, 3)
```

</div>


<br>
<br>

---

<div class="function_block">

## `tag: `<code class="type">Read</code>

Copies a read and appends a string to the end of its description line. A space is added.

<table style="margin-left: 0; width: 100%">

<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>read: </code><code class="type">Read</code></td><td>The read to tag.</td>
</tr>
<tr>
<td><code>tag: </code><code class="type">Str</code></td><td>The string to append to the read's description line.</td>
</tr>
<tr>
<td><code><i>prefix</i>: </code><code class="type">Str</code><code> = ' '</code></td><td>A string inserted between the existing description line and the new tag.</td>
</tr>
</table>

```matchbox
# trim off the first 10 bases,
# and tag the read with their sequence
if read is [bc:|10| rest:_] =>
    rest.tag('barcode={bc.seq}')
        .out!('out.fa')
```

</div>

<br>
<br>

---

<div class="function_block">

## `translate: `<code class="type">Str</code>

Translates a string from nucleotide to protein sequence. Naively assumes that you've given it a string representing a valid sequence of nucleotides. When the input string contains an invalid codon (including if the input string contains characters aside from `A`, `C`, `T` and `G`), a `'?'` character is produced. 

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>seq: </code><code class="type">Str</code></td><td>The sequence to translate.</td>
</tr>
</table>

```matchbox
seq = AGCCCTCCAGGACAGGCTGCATCAGAAGAG
# will translate to SPPGQAASEE
prot = translate(seq)
```

</div>


<br>
<br>

---

<div class="function_block">

## `str_concat:` <code class="type">Str</code>

Concatenates two strings. 

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s0: </code><code class="type">Str</code></td><td>The first string.</td>
</tr>
<tr>
<td><code>s1: </code><code class="type">Str</code></td><td>The second string.</td>
</tr>
</table>

```matchbox
s0 = 'hello'
s1 = 'world'

# all equivalent
a = str_concat(s0, s1)
b = '{s0}{s1}'
c = 'helloworld'
```

</div>



<br>
<br>

---

<div class="function_block">

## `concat:` <code class="type">Read</code>

Concatenates two reads together. 

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>r1: </code><code class="type">Read</code></td><td>The first read.</td>
</tr>
<tr>
<td><code>r2: </code><code class="type">Read</code></td><td>The second read.</td>
</tr>
</table>

```matchbox
# locate a known sequence, and cut it out -
# concatenating everything before the sequence
# onto everything after it
if read is [before:_ AGCTAGTCG after:_] => 
    before
        .concat(after)
        .out!('primer_excluded.fq')
```

```matchbox
# glue together the forward read1 and the reverse read2
read.r1.concat(-read.r2).out!('glued.fq')
```

</div>

<br>
<br>

---

<div class="function_block">

## `csv:` <code class="type">[Record]</code>

Opens a CSV and produces a list of records. The field names of each record correspond to the header names of the CSV, and the values correspond to the values found on each row. This processing occurs once, at the start of execution. The entire CSV is loaded into memory.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>filename: </code><code class="type">Str</code></td><td>The CSV file to open.</td>
</tr>
</table>

```matchbox
primer = AGCTAGTCGATGC
bcs = csv('my_barcodes.csv')

if read is [_ primer bc.sequence _] for bc in bcs =>
    read.tag('barcode={bc.barcode_name}')
        .out!('demultiplexed.fq')
```


</div>

<br>
<br>

---

<div class="function_block">

## `tsv:` <code class="type">[Record]</code>

Opens a TSV and produces a list of records. The field names of each record correspond to the header names of the TSV, and the values correspond to the values found on each row. This processing occurs once, at the start of execution. The entire TSV is loaded into memory.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>filename: </code><code class="type">Str</code></td><td>The TSV file to open.</td>
</tr>
</table>

```matchbox
primer = AGCTAGTCGATGC
bcs = tsv('my_barcodes.tsv')

if read is [_ primer bc.sequence _] for bc in bcs =>
    read.tag('barcode={bc.barcode_name}')
        .out!('demultiplexed.fq')
```


<br>
<br>

---

<div class="function_block">

## `fasta:` <code class="type">[Read]</code>

Opens a FASTA and produces a list of reads, each one containing `seq`, `id` and `desc` fields. This processing occurs once, at the start of execution. The entire TSV is loaded into memory.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>filename: </code><code class="type">Str</code></td><td>The TSV file to open.</td>
</tr>
</table>

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read is [_ primer bc.seq _] for bc in bcs =>
    read.tag('barcode={bc.id}')
        .out!('demultiplexed.fq')
```

</div>

<br>
<br>

---

<div class="function_block">

## `find_first:` <code class="type">Num</code>

Searches for a substring within a string. If the substring is present, returns the first 0-based position within the string where the substring could be found. If the substring is not present, returns `-1`.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s0: </code><code class="type">Str</code></td><td>The string to search through.</td>
</tr>
<tr>
<td><code>s1: </code><code class="type">Str</code></td><td>The substring to search for in <code>s0</code>.</td>
</tr>
</table>

```matchbox
# get the protein translation of the read
protein = read.seq.translate()
# find the first Cysteine amino acid in the sequence
location = protein.find_first('C')
# if a Cysteine could be found, trim the read,
# only keeping everything after the first Cys codon
if location != -1 =>
    if read is [|location*3| rest:_] =>
        rest.out!('trimmed.fq')
```

</div>


<br>
<br>

---

<div class="function_block">

## `find_last:` <code class="type">Num</code>

Searches for a substring within a string. If the substring is present, returns the last 0-based position within the string where the substring could be found. If the substring is not present, returns `-1`.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s0: </code><code class="type">Str</code></td><td>The string to search through.</td>
</tr>
<tr>
<td><code>s1: </code><code class="type">Str</code></td><td>The substring to search for in <code>s0</code>.</td>
</tr>
</table>

```matchbox
# get the protein translation of the read
protein = read.seq.translate()
# find the last Cysteine amino acid in the sequence
location = protein.find_last('C')
# if a Cysteine could be found, trim the read,
# only keeping everything before the last Cys codon
if location != -1 =>
    if read is [rest:_ |location*3|] =>
        rest.out!('trimmed.fq')
```

</div>

<br>
<br>

---

<div class="function_block">

## `to_upper:` <code class="type">Str</code>

Converts a string to upper-case. Non-alphabetic characters are unaffected.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to convert to upper-case.</td>
</tr>
</table>

```matchbox
loud_hello = 'hello'.to_upper()
```

</div>


<br>
<br>

---

<div class="function_block">

## `to_lower:` <code class="type">Str</code>

Converts a string to lower-case. Non-alphabetic characters are unaffected.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to convert to lower-case.</td>
</tr>
</table>

```matchbox
quiet_hello = 'HELLO'.to_lower()
```



<br>
<br>

---

<div class="function_block">

## `describe:` <code class="type">Str</code>

Searches for a set of sequences within a read's `seq` field, and returns the pattern which most precisely describes the read, as a `Str`. Very useful for exploring the arrangement of known primers and static regions in your data!

Each search term is searched within the read. If `reverse_complement` is true, the reverse-complement sequences are also searched. Edit distance is allowed in proportion to the length of each search term sequence and the `error` parameter. Any matches are then concatenated together to produce a pattern string which describes the read in terms of the searched sequences.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>read: </code><code class="type">Read</code></td><td>The read to describe.</td>
</tr>
<tr>
<td><code>search_terms: </code><code class="type">Record</code></td><td>The set of terms to search for. Each field of the struct must have a <code class="type">Str</code> value, which represents the sequence to search for. Each field's name is used as a label for the sequence in the pattern.</td>
</tr>
<tr>
<td><code><i>reverse_complement</i>: </code><code class="type">Bool</code><code> = false</code></td><td>Whether to additionally search for the reverse complement of each search term.</td>
</tr>
<tr>
<td><code><i>error</i>: </code><code class="type">Num</code><code> = 0</code></td><td>Error rate proportion to allow when searching for each search term.</td>
</tr>
</table>

```matchbox
read.describe(
    { tso = AGCATGCTGATG, rtp = GATCGTACGTGTTG },
    reverse_complement = true
) |> count!()
```

</div>



<br>
<br>

---

<div class="function_block">

## `contains:` <code class="type">Bool</code>

Checks whether a value is present in a list. 

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>list: </code><code class="type">[Any]</code></td><td>The list to search through.</td>
</tr>
<tr>
<td><code>val: </code><code class="type">Any</code></td><td>The value to search for.</td>
</tr>
</table>

```matchbox
# we have a one-column CSV, 
# containing the names of a subset of 
# reads we're interested in
names = csv('names.csv')

# if the read is on the list,
# include it in the subset
if names.contains({name = read.id}) =>
    read |> out!('subset.fq')
```

</div>

<br>
<br>

---

<div class="function_block">

## `distance:` <code class="type">Num</code>

Calculates the global edit distance between two strings.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s0: </code><code class="type">Str</code></td><td>The first string.</td>
</tr>
<tr>
<td><code>s1: </code><code class="type">Str</code></td><td>The second string.</td>
</tr>
</table>

```matchbox
# equal to 1
d = distance('cat', 'bat')
```

</div>


<br>
<br>

---

<div class="function_block">

## `to_str:` <code class="type">Str</code>

Converts any value to a <code class="type">Str</code>. Equivalent to formatting the value in a string literal (e.g. `'{val}'`).

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>v: </code><code class="type">Any</code></td><td>The value to convert.</td>
</tr>
</table>

```matchbox
# convert the length of the sequence (a Num) into a Str
length_str = read.seq.len().to_str()
# calculate the length of the Str
number_of_digits = length_str.len()
```

</div>

<br>
<br>

---

<div class="function_block">

## `to_num:` <code class="type">Num</code>

Parses a <code class="type">Str</code> into a <code class="type">Num</code>. When given a value that can't be parsed into a floating-point number, throws an error.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to parse.</td>
</tr>
</table>

```matchbox
'100'.to_num()
```

</div>

<br>
<br>

---

<div class="function_block">

## `stdout!:` <code class="type">Effect</code>

Prints any value directly to `stdout`. 

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>val: </code><code class="type">Any</code></td><td>The value to print.</td>
</tr>
</table>

```matchbox
# print out each read's ID
stdout!(read.id)
```

</div>
<br>
<br>

---

<div class="function_block">

## `out!:` <code class="type">Effect</code>

Prints any value to a file. Based on the filename, the format of the output will be inferred. When the filetype denotes FASTA, FASTQ or SAM format, the value provided must be a read containing the following fields:

<table>
<th>Input format</th>
<th>Fields</th>

<tr>
<td>FASTA<br>(<code>.fa</code>, <code>.fasta</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>id: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
        </tr>
    </table>
</td>
</tr>
<tr>
<td>FASTQ<br>(<code>.fq</code>, <code>.fastq</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>id: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>qual: </code><code class="type">Str</code></td>
        </tr>
    </table>
</td>
</tr>
<tr>
<td>SAM<br>(<code>.sam</code>, <code>.bam</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>id | qname: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>flag: </code><code class="type">Num</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>rname: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>pos: </code><code class="type">Num</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>mapq: </code><code class="type">Num</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>cigar: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>rnext: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>pnext: </code><code class="type">Num</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>tlen: </code><code class="type">Num</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>qual: </code><code class="type">Str</code></td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
        </tr>
    </table>
</td>
</tr>
</table>

If the file is any other format, it is treated as a plain text file, and any values sent to it are simply printed directly and separated by newlines.

A file is only created when the first

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>val: </code><code class="type">Any</code></td><td>The value to print.</td>
</tr>
<tr>
<td><code>filename: </code><code class="type">Str</code></td><td>The name of the file to write to.</td>
</tr>
</table>

```matchbox
if read is [|10| rest:_] => rest.out!('trimmed.fq')
```

</div>

<br>
<br>

---

<div class="function_block">

## `count!:` <code class="type">Effect</code>

Collects all of the values sent to `count!` across all of the reads. Tallies up the number of times each unique value is sent. At the end of execution, prints out a table of counts for each value. 

Can be used for quantifying how many reads match certain criteria, or for counting occurrences of sequences such as barcodes. To generate multiple seperate counts tables from a single pass, the `name` parameter can be used to identify different global counts variables. Each `name` will correspond to a fresh table.

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>val: </code><code class="type">Any</code></td><td>The value to be counted.</td>
</tr>
<tr>
<td><code><i>name</i>: </code><code class="type">Str</code><code> = 'default'</code></td><td>The name of the global counts matrix to store the value into. Multiple tables of counts can be generated, using different names.</td>
</tr>
</table>

```matchbox
# count each read towards the 'total'
count!('total')

# for each read with a length over 1000,
# count it towards the 'long' total
if read.len() > 1000 => count!('long')
```

```matchbox
primer = AGCTAGCTGA

# all reads count to the 'total' in the 'read types' table
count!('total', name='read types')

# find the primer, and then count the 
# occurrences of unique 10-bp sequences following it
if read is [_ primer bc:|10| _] => {
    # reads with the primer are counted
    # in the 'read types' table
    count!('found primer', name='read types')

    # unique barcodes are tracked by the 'barcodes' table
    bc.seq.count!(name = 'barcodes')
}
```

</div>


<br>
<br>

---

<div class="function_block">

## `average!:` <code class="type">Effect</code>

Collects all of the numeric values sent to `average!` across all of the reads. To simultaneously calculate multiple averages, the `name` parameter can be used. At the end of execution, prints out the mean and variance. 

To avoid having to store all the numeric values, variance is calculated using [Welford's online algorithm](https://en.wikipedia.org/wiki/Algorithms_for_calculating_variance).

<table style="margin-left: 0; width: 100%">
<th style="width: 11em">Parameter</th>
<th>Description</th>
<tr>
<td><code>num: </code><code class="type">Num</code></td><td>The number to contribute to the average.</td>
</tr>
<tr>
<td><code><i>name</i>: </code><code class="type">Str</code><code> = 'default'</code></td><td>The name of the global variable to store the average into. Multiple averages can be calculated at once, using different names.</td>
</tr>
</table>

```matchbox
# calculate the average length of all sequences
read.seq.len().average!()
```

```matchbox
# calculate average sequence length
read.seq.len().average!(name='sequence length')

primer = AGCTAGCTGA

# for reads that contain the primer,
# calculate the average position at which it occurs
if read is [before:_ primer _] =>
    before.seq.len().average!(name='primer position')
```

</div>
