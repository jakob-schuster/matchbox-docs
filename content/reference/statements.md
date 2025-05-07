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

The value of an expression can be sent to an output.

Summary statistics, trimmed read files and the standard output stream are all examples of outputs in *matchbox*. Outputs accumulate across all of the input reads. 

```matchbox
'total reads' |> counts()

if read.seq.len() < 100 =>
    'very short' |> counts()
```

```matchbox
if read is [first:|10| rest:_] => {
    first |> file('first_10bp.fq')
    rest |> file('rest.fq')
}
```

```matchbox
read.seq.len() |> average()
```

---

## Branching with conditionals

`if` statements can be used to ensure some statements only execute when certain conditions are met.

A <code class="type">Bool</code> expression can be used.

```matchbox
if len(read.seq) > 1000 => read.out!('long.fa')
```

Multiple branches can be included, separated by semicolons or newlines. The branches are tried in order, and only the first successful branch is executed.

```matchbox
if len(read.seq) > 1000 => read |> file('long.fa')
   len(read.seq) > 100 => read |> file('medium.fa')
```

Alternatively, pattern matching can be performed on an expression using the `is` keyword. 

```matchbox
if read.name is 
    'read1' => {
        '' |> counts()
        read.seq |> stdout()
    }
    _ => 'other reads' |> counts()
```

To perform pattern matching on reads, which supports error tolerance and allows for slices of reads to be extracted, special read pattern syntax can be used. For more information, see [Patterns](../patterns/).