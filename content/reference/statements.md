+++
title = "Statements"
template = "page.html"
weight = 0
+++

A *matchbox* script is a sequence of statements. 

Each time a read is processed, all the statements are executed from top to bottom.

---

## Assigning variables

A variable name can be assigned to the value of an expression.

Variable names can include letters, numbers and underscores. A name must start with a letter or an underscore. Variable names must be unique, and can't be overwritten.

```matchbox
a = 4
b = 'the value of a is {a}'
```

---

## Producing output

Some expressions produce output, which accumulates across all of the input reads. Summary statistics, trimmed read files and the standard output stream are all examples of outputs in *matchbox*. 

Functions that produce output have exclamation marks after their name. Only expressions that produce output are allowed at the statement level.

```matchbox
# count every read towards a tally of 'total reads'
count!('total reads')

# when reads are shorter than 100 bp, 
# count them towards a tally of 'very short' reads
if read.seq.len() < 100 {
    count!('very short')
}

# (both of these tallies will be printed
# after the reads have been processed)
```

```matchbox
if read matches [first:|10| rest:_] => {
    # send the first 10 bp of each read to 'first_10bp.fq'
    first.out!('first_10bp.fq')
    # and send the remaining part of the read to 'rest.fq'
    rest.out!('rest.fq')
}
```

```matchbox
# the pipe operator |> can be used as 
# an alternative to the dot . when calling functions
read.seq.len() |> average!()
```

---

## Branching with conditionals

`if` statements can be used to ensure some statements only execute when certain conditions are met.

A <code class="type">Bool</code> expression can be used in a conditional.

```matchbox
if len(read.seq) > 1000 {
    read.out!('long.fa')
}
```

`if` statements can have an `else` branch.

```matchbox
if len(read.seq) > 1000 {
    read |> out!('long.fa')
} else if len(read.seq) > 100 {
    read |> out!('medium.fa')
} else {
    read |> out!('short')
}
```

Alternatively, pattern matching can be performed on an expression using the `matches` keyword. Reads can be pattern-matched with error tolerance, and trimmed regions can be extracted. For more information, see [Patterns](../patterns/).

```matchbox
# trim off polyA tails
if read matches [_ AAAAAAAA after:_] => after.out!('trimmed.fa')
```