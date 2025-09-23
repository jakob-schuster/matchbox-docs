+++
title = "Patterns"
template = "page.html"
weight = 1
+++

*matchbox*'s key feature is the ability to pattern-match on sequences, while tolerating mismatches and extracting particular slices of reads. 

With pattern matching, *matchbox* can be used to filter, trim or demultiplex reads. 

---

## Branches

Pattern-matching takes place in the branches of an `if` .. `matches` statement:

```matchbox
# trim off polyA tails
if read matches {
    [_ AAAAAAAA after:_] => after.out!('trimmed.fa')
    [_] => read.out!('no_polya.fa')
}
```

Branches are separated by semicolons or newlines. Each branch is tried in order, until one is successful. Only the first successful branch will be executed. 

<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top; width:2em;text-align:center;padding-top:0.5em">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
            When a pattern matches multiple places in a single read (e.g. if the pattern above is used, and a read contains multiple polyA regions), then the body of the branch is executed for all instances of the match. This may cause the same read to produce multiple outputs.
        </td>
    </tr>
</table>
</div>

If you want to trigger multiple statements from a single branch, use curly braces:

```matchbox
# trim off polyA tails
if read matches {
    [_ AAAAAAAA after:_] => {
        after.out!('trimmed.fa')
        count!('polyA reads')
    }
    [_] => count!('other reads')
}
```

---

## Patterns and regions

A pattern contains a series of regions enclosed in square brackets `[]`. Patterns are comprised of several basic regions:

<table>
    <th>Name</th>
    <th>Syntax</th>
    <th>Description</th>
    <tr>
        <td>Wild</td>
        <td><code>_</code></td>
        <td>Matches any number of any nucleotides.</td>
    </tr>
    <tr>
        <td>Fixed-length</td>
        <td><code>|</code><i>n</i><code>|</code></br><br><code>|</code><i>n</i><code>:</code><i>r</i><code>|</code></td>
        <td>Matches exactly <i>n</i> nucleotides, where <i>n</i> is an expression of type <code class="type">Num</code>. <br><br>Optionally, may contain another region <i>r</i> inside it.</td>
    </tr>
    <tr>
        <td>Known sequence</td>
        <td><i>s</i></td>
        <td>Matches a known sequence <i>s</i>, where <i>s</i> is an expression of type <code class="type">Str</code>. This may be a sequence literal such as <code>AAAAAAAA</code>, or a variable name such as <code>primer</code>. <br><br>Allows <i>d</i> base pairs of edit distance, where <i>d</i> is the length of <i>s</i> times the global error rate.</td>
    </tr>
    <tr>
        <td>Known sequence with specified error rate</td>
        <td><i>s</i><code>~</code><i>n</i></td>
        <td>Matches a known sequence <i>s</i>, while overriding the error rate with <i>n</i>. <i>s</i> is an expression of type <code class="type">Str</code>, and <i>n</i> is an expression of type <code class="type">Num</code>. <br><br>Allows <i>d</i> base pairs of edit distance, where <i>d</i> is the length of <i>s</i> times the specified error rate <i>n</i>.</td>
    </tr>
    <tr>
        <td>Named</td>
        <td><i>a</i><code>:</code><i>r</i></td>
        <td>Matches against the inner region <i>r</i>, and assigns the name <i>a</i> to refer to the matched slice of the read.</td>
    </tr>
    <tr>
        <td>Grouped</td>
        <td><code>(</code><i>r*</i><code>)</code></td>
        <td>A sub-pattern, consisting of a series of regions.</td>
    </tr>
</table>

The whole pattern acts like a schematic for the read, representing the whole read from left to right.

Here are some examples of patterns, and how to read them:

<table>
    <th style="width:15em">Example</th>
    <th>Description</th>
    <tr>
        <td><code>[_]</code></td>
        <td>Any read, of any length.</td>
    </tr>
    <tr>
        <td><code>[|10|]</code></td>
        <td>A read of exactly 10 bp.</td>
    </tr>
    <tr>
        <td><code>[|10| _]</code></td>
        <td>A read with at least 10 bp at the start.</td>
    </tr>
    <tr>
        <td><code>[first:|10| _]</code></td>
        <td>A read with at least 10 bp at the start. Extract these first 10 bp, and call the slice <code>first</code>.</td>
    </tr>
    <tr>
        <td><code>[first:|10| rest:_]</code></td>
        <td>A read with at least 10 bp at the start. Extract these first 10 bp, and call the slice <code>first</code>. Extract the rest of the read, and call it <code>rest</code>.</td>
    </tr>
    <tr>
        <td><code>[_ AAAAAAAAAA _]</code></td>
        <td>A read containing the sequence <code>AAAAAAAAAA</code>.</td>
    </tr>
    <tr>
        <td><code>[_ AAAAAAAAAA~0.2 _]</code></td>
        <td>A read containing the sequence <code>AAAAAAAAAA</code>, with error rate 0.2.</td>
    </tr>
    <tr>
    <tr>
        <td><code>[fst:_ AAAAAAAAAA _]</code></td>
        <td>A read containing the sequence <code>AAAAAAAAAA</code>. Extract the bases before the sequence, and call them <code>fst</code>.</td>
    </tr>
    <tr>
        <td><code>[_ AAAA _ TTTT _]</code></td>
        <td>A read containing the sequence <code>AAAA</code>, and later, <code>TTTT</code>.</td>
    </tr>
    <tr>
        <td><code>[_ AAAA mid:_ TTTT _]</code></td>
        <td>A read containing the sequence <code>AAAA</code>, and later, <code>TTTT</code>. Take the bases in between and call the slice <code>mid</code>.</td>
    </tr>
    <tr>
        <td><code>[|40:(_ AAAA _)| _]</code></td>
        <td>A read with at least 40 bp, which contains the sequence <code>AAAA</code> in the first 40 bp.</td>
    </tr>
    <tr>
        <td><code>[|40:(_ AAAA x:_)| _]</code></td>
        <td>A read with at least 40 bp, which contains the sequence <code>AAAA</code> in the first 40 bp. Extract the remaining bases from after the sequence <code>AAAA</code> up to the 40th base, and call the slice <code>x</code>.</td>
    </tr>
</table>


<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top; width:2em;text-align:center;padding-top:0.5em">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
            A fixed-length pattern <code>|n|</code> cannot be placed between two wild regions. It must be anchored on at least one side by either end of the read, or by a known sequence. Otherwise, a pattern like <code>[_ |10| _]</code> could be used to denote "all possible selections of 10 bp from the read". Currently, <i>matchbox</i> rejects this as it would be computationally expensive and probably confusing.
        </td>
    </tr>
</table>
</div>

## Parameters using `for`

Patterns can also be parameterised, useful when demultiplexing or matching against a list of known sequences. 

```matchbox
primer = AGCTAGTCGATGC
bcs = fasta('my_barcodes.fa')

if read matches [_ primer bc.seq _] for bc in bcs =>
    read.tag('barcode={bc.id}')
        .out!('demultiplexed.fq')
```

Using the syntax `for bc in bcs`, a name `bc` is bound to a single value from the list of values `bcs`. The name `bc` can then be used in the body of the branch.

<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top; width:2em;text-align:center;padding-top:0.5em">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
            When multiple values from the list could be used to satisfy the pattern (e.g. when multiple barcodes match in a read), the body of the branch is executed for all values that satisfy the pattern.
        </td>
    </tr>
</table>
</div>
