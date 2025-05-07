+++
title = "Fixed-length regions"
template = "page.html"
weight = 1
+++

Sometimes we want to extract a fixed number of bases from a read. Fixed-length regions can be specified using vertical bars:

```matchbox
if read is [first_five:|5| _] => 
    first_five.out!('starts.fq')
```

Fixed-length regions can be taken from either side of the read:

```matchbox
if read is [_ last_five:|5|] => 
    last_five.out!('ends.fq')
```

If we also want to separately extract the rest of the sequence:

```matchbox
if read is [start:|5| rest:_ end:|5|] => {
    start.out!('starts.fq')
    rest.out!('rest.fq')
    end.out!('ends.fq')
}
```

## Fixed-length regions around known sequences

Fixed-length regions can also be extracted from either side of known sequences:

```matchbox
primer = AGCTAGCTGATCGATGAGCT

if read is [_ primer umi:|8| _] =>
    read.tag('umi={umi.seq}')
        .out!('with_umis.fq')
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
            The <code>tag</code> function allows you to easily append to a read's metadata. See <a href="https://en.wikipedia.org/wiki/Levenshtein_distance">Working with metadata</a>. 
        </td>
    </tr>
</table>
</div>
