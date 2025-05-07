+++
title = "Searching for sequences"
template = "page.html"
weight = 0
+++

Often, we have known sequences such as primers that we want to search for in our reads.

```matchbox
primer = AGCTAGCTGATGCTAGCTGG

if read is 
    [_ primer _] => count!('found primer')
    [_] => count!('other')
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
            The global error rate parameter will be used when searching for sequences. In this case, an error rate of 0.2 with this 20-bp primer sequence will allow a <a href="https://en.wikipedia.org/wiki/Levenshtein_distance">Levenshtein distance</a> of 4 bp. 
        </td>
    </tr>
</table>
</div>

## Reverse complementation

If we also want to search for the reverse complement of the primer:

```matchbox
primer = AGCTAGCTGATGCTAGCTGG

if read is 
    [_ primer _] => count!('found primer')
    [_ -primer _] => count!('found reverse primer')
    [_] => count!('other')
```

## Filtering

If we want to filter our reads into different files:

```matchbox
primer = AGCTAGCTGATGCTAGCTGG

if read is 
    [_ primer _] => read.out!('primer.fq')
    [_ -primer _] => read.out!('rev_primer.fq')
    [_] => read.out!('other.fq')
```

<div class="info_block">
<table>
    <tr>
        <td  style="vertical-align: top">
        <span class="material-symbols-outlined">
        info
        </span>
        </td>
        <td>
        If you're expecting FASTQ output, like in the line <code>out!('primer.fq')</code>, then you can't provide FASTA reads as input, because they don't have a quality score. Similarly, SAM output can only be produced from SAM input, since they carry more metadata than FASTQ. 
        </td>
    </tr>
</table>
</div>

## Searching for multiple sequences

Often, we have multiple known sequences we want to search for.

```matchbox
primer1 = AGCTAGCTGATGCTAGCTGG
primer2 = AGCTAGCTGATGCTAGCTGG

if read is 
    [_ primer1 _ primer2 _] => count!('both primers')
    [_] => count!('one or neither')
```

## Discovering arrangements of primers in reads 

Instead of writing out patterns for each primer, we can use the `describe` function to get counts for each arrangement of the primers in the data:

```matchbox
read.describe({
    primer1 = AGCTAGCTGATGCTAGCTGG
    primer2 = AGCTAGCTGATGCTAGCTGG
}).count!()
```

The optional arguments `reverse_complement` and `error` can be used to customise this search:

```matchbox
primers = {
    primer1 = AGCTAGCTGATGCTAGCTGG
    primer2 = AGCTAGCTGATGCTAGCTGG
}

# search only for exact matches, and search for both 
# forward and reverse-complement sequences
read.describe(
    primers, reverse_complement = true, error = 0.0
).count!()
```
