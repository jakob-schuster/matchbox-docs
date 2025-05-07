+++
title = "Working with metadata"
template = "page.html"
weight = 4
+++

## Read fields

Reads have several fields that store metadata. Which fields are available depends on the type of input reads you provide.


<table>
<th>Input format</th>
<th>Fields</th>

<tr>
<td>FASTA<br>(<code>.fa</code>, <code>.fasta</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
            <td style="width:13em">The sequence of the read.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>id: </code><code class="type">Str</code></td>
            <td style="width:13em">The read ID. Everything from <code>></code> to the first whitespace in the description line.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
            <td style="width:13em">The description line. Everything after the read ID.</td>
        </tr>
    </table>
</td>
</tr>
<tr>
<td>FASTQ<br>(<code>.fq</code>, <code>.fastq</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
            <td style="width:13em">The sequence of the read.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>id: </code><code class="type">Str</code></td>
            <td style="width:13em">The read ID. Everything from <code>@</code> to the first whitespace in the description line.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
            <td style="width:13em">The description line. Everything after the read ID.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>qual: </code><code class="type">Str</code></td>
            <td style="width:13em">The quality string.</td>
        </tr>
    </table>
</td>
</tr>
<tr>
<td>SAM<br>(<code>.sam</code>, <code>.bam</code>)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>id | qname: </code><code class="type">Str</code></td>
            <td style="width:13em">The read ID. Defaults to <code>'*'</code> if unavailable.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>flag: </code><code class="type">Num</code></td>
            <td style="width:13em">Combination of <a href="https://broadinstitute.github.io/picard/explain-flags.html">bitwise flags</a>.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>rname: </code><code class="type">Str</code></td>
            <td style="width:13em">Name of the reference sequence to which the read is aligned. Defaults to <code>'*'</code> if unavailable or unmapped.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>pos: </code><code class="type">Num</code></td>
            <td style="width:13em">1-based leftmosed mapping position of the first CIGAR operation that consumes a reference base. Defaults to <code>0</code> if unmapped.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>mapq: </code><code class="type">Num</code></td>
            <td style="width:13em">Mapping quality.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>cigar: </code><code class="type">Str</code></td>
            <td style="width:13em">CIGAR string.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>rnext: </code><code class="type">Str</code></td>
            <td style="width:13em">Reference sequence name of the primary alignment of the next read in the template. Defaults to <code>'*'</code> if unavailable or unmapped.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>pnext: </code><code class="type">Num</code></td>
            <td style="width:13em">1-based position of the primary alignment of the next read in the template. Defaults to <code>0</code> if unavailable.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>tlen: </code><code class="type">Num</code></td>
            <td style="width:13em">Signed observed template length. Defaults to <code>0</code> if unavailable.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>seq: </code><code class="type">Str</code></td>
            <td style="width:13em">The sequence of the read.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>qual: </code><code class="type">Str</code></td>
            <td style="width:13em">The quality string.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>desc: </code><code class="type">Str</code></td>
            <td style="width:13em">The optional fields, as a single string.</td>
        </tr>
    </table>
</td>
</tr>
<tr>
<td>Paired<br>(any formats)</td><td>
    <table>
        <tr>
            <td style="width:11em"><code>r1: </code><code class="type">Read</code></td>
            <td style="width:13em">The first read, from the file given as mandatory argument.</td>
        </tr>
        <tr>
            <td style="width:11em"><code>r2: </code><code class="type">Read</code></td>
            <td style="width:13em">The second read, from the file given via the <code>--paired-with</code> optional argument.</td>
        </tr>
    </table>
</td>
</tr>
</table>

## Reverse-complementing

Reads can be reverse-complemented. For FASTA reads, this reverse-complements the sequence. For FASTQ and SAM reads, it additionally reverses the quality score, so that the bases still correctly correspond to quality score characters. 

```matchbox
# reverse complement each read
-read.out!('reversed.fq')
```

## Tagging

Information can be added to the description line of reads, such as a UMI or barcode, or any other data derived from processing with *matchbox*. 

```matchbox
# tag each read with its length
read.tag('length={read.seq.len()}')
```

<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top; width:2em;text-align:center;padding-top:0.5em">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
            The string given to <code>tag</code> is appended to the read's <code>desc</code>, separated by a space. To change this prefix (e.g. when adding an optional field to a SAM read, which are tab-delimited), the optional argument <code>prefix</code> can be set. 
        </td>
    </tr>
</table>
</div>
