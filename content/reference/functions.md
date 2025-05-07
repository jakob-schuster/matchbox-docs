+++
title = "Functions"
template = "page.html"
weight = 3
+++

*matchbox* functions are used to manipulate and transform data.

## Defining functions

New functions can be defined using function literal syntax. Arguments must be declared with their types, in parentheses, separated by commas. The body of the function comes after an arrow `=>`. The function's return type is inferred from its body.

Variable names can be assigned to functions, just like any other value.

```matchbox
# a function that  numbers
f = (n1: Num, n2: Num) => n1 * 3

# a function which formats the result of f into a Str
g = (n: Num) => '{n} times two equals {f(n)}'
```

## Applying functions

A function can be applied by writing the function name followed by parentheses enclosing a comma-separated list of arguments.

```matchbox
n = len(read.seq)
```

Alternatively, a function can be applied with the first argument in front and a dot before the function name. All of the remaining arguments are still written inside the parentheses.

```matchbox
# equivalent
n = read.seq.len()
```

Similarly, the pipe operator `|>` can also be used to apply functions.

```matchbox
# also equivalent
n = read.seq |> len()

# useful when chaining functions together!
read 
    |> tag('length={len(read.seq)}') 
    |> out!('file.fq')
```

---

# Built-in functions

Most of the time, users will be applying built-in *matchbox* functions.

This list explains every built-in function with examples.

---

<div class="function_block">

## `len(): `<code class="type">Num</code>

Calculates the length of a string.

<table>
<th>Parameter</th>
<th>Description</th>
<tr>
<td><code>s: </code><code class="type">Str</code></td><td>The string to calculate the length of.</td>
</tr>
</table>

**Example**

```matchbox
# get the average read length
read.seq.len().average!()
```

</div>

---

<div class="function_block">

## `tag(): `<code class="type">Read</code>

Copies a read and appends a string to the end of its description line. A space is added.

<table>
<th>Parameter</th>
<th>Description</th>
<tr>
<td><code>read: </code><code class="type">Read</code></td><td>The read to tag.</td>
</tr>
<tr>
<td><code>tag: </code><code class="type">Str</code></td><td>The string to append to the read's description line.</td>
</tr>
</table>

**Example**

```matchbox
# trim off the first 10 bases,
# and tag the read with their sequence
if read is [bc:|10| rest:_] =>
    rest.tag('barcode={bc.seq}')
        .out!('out.fa')
```

</div>

---

<div class="function_block">

## `describe(): `<code class="type">Str</code>
Searches for a set of sequences within a read's `seq` field, and returns the pattern which most precisely describes the read, as a `Str`.

<table>
<th>Parameter</th>
<th>Description</th>
<tr>
<td><code>read: </code><code class="type">Read</code></td><td>The read to describe.</td>
</tr>
<tr>
<td><code>search_terms: </code><code class="type">{..}</code></td><td>The set of terms to search for. Each field of the struct must have a <code>Str</code> value. Each field's name is used as a label in the pattern.</td>
</tr>
<tr>
<td><code><i>reverse_complement</i>: </code><code class="type">Bool</code><code> = false</code></td><td>Whether to additionally search for the reverse complement of each search term.</td>
</tr>
<tr>
<td><code><i>error</i>: </code><code class="type">Num</code><code> = 0</code></td><td>Error rate proportion to allow when searching for each search term.</td>
</tr>
</table>

**Example**

```matchbox
read.describe(
    { tso = AGCATGCTGATG },
    reverse_complement = true
) |> count!()
```
</div>

### `to_upper(s: Str): Str`
Converts a string `s` to upper case. Non-alphabetic characters are unchanged.
### `to_lower(s: Str) -> Str`
Converts a string `s` to lower case. Non-alphabetic characters are unchanged.

---

# Output functions

Some functions produce output, which accumulate across all of the reads. This is the way to get results out of <i>matchbox</i>. These functions have 

---

<div class="function_block">

## `out!(): `<code class="type">Effect</code>
Searches for a set of sequences within a read's `seq` field, and returns the pattern which most precisely describes the read, as a `Str`.

<table>
<th>Parameter</th>
<th>Description</th>
<tr>
<td><code>read: </code><code class="type">Read</code></td><td>The read to describe.</td>
</tr>
<tr>
<td><code>search_terms: </code><code class="type">{..}</code></td><td>The set of terms to search for. Each field of the struct must have a <code>Str</code> value. Each field's name is used as a label in the pattern.</td>
</tr>
<tr>
<td><code><i>reverse_complement</i>: </code><code class="type">Bool</code><code> = false</code></td><td>Whether to additionally search for the reverse complement of each search term.</td>
</tr>
<tr>
<td><code><i>error</i>: </code><code class="type">Num</code><code> = 0</code></td><td>Error rate proportion to allow when searching for each search term.</td>
</tr>
</table>

**Example**

```matchbox
read.describe(
    { tso = AGCATGCTGATG },
    reverse_complement = true
) |> count!()
```
</div>


---

# Operators

Operators are a special group of built-in functions which have symbols as their names, and which are applied between their arguments.

```matchbox
# + and > are both operators
if 10 + 2 > 11 => 'basic maths' |> stdout()
```

Some operators bind more tightly than others. The full list of operators is given below, from tightest to loosest precedence.

<table>
<th>Precedence</th>
<th>Operators</th>

<tr>
<td>0</td><td>
    <i>All literal values</i><br>
    <i>Function / method calls</i><br>
    <i>Accessing fields of records</i><br>
</td>
<td>
</td>
</tr>

<tr>
<td>1</td><td>
    <table>
        <tr>
            <td style="width:9em"><code> -</code><code class="type">Num</code></td>
            <td style="width:12em">Unary negation</td>
        </tr>
        <tr>
            <td><code> -</code><code class="type">Str</code></td>
            <td>Division</td>
        </tr>
    </table>
</td>
</tr>

<tr>
<td>2</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Num</code><code> * </code><code class="type">Num</code></td>
            <td style="width:12em">Multiplication</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> / </code><code class="type">Num</code></td>
            <td>Division</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> % </code><code class="type">Num</code><br></td>
            <td>Modulo</td>
        </tr>
    </table>
</td>

</tr>

<tr>
<td>3</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Num</code><code> + </code><code class="type">Num</code></td>
            <td style="width:12em">Addition</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> - </code><code class="type">Num</code></td>
            <td>Subtraction</td>
        </tr>
    </table>

</td>
</tr>


<tr>
<td>4</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Num</code><code> < </code><code class="type">Num</code></td>
            <td style="width:12em">Less than</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> > </code><code class="type">Num</code></td>
            <td>Greater than</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> <= </code><code class="type">Num</code></td>
            <td>Less than or equal</td>
        </tr>
        <tr>
            <td><code class="type">Num</code><code> >= </code><code class="type">Num</code></td>
            <td>Greater than or equal</td>
        </tr>
    </table>
</td>
</tr>

<tr>
<td>5</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Any</code><code> == </code><code class="type">Any</code></td>
            <td style="width:12em">Equality</td>
        </tr>
        <tr>
            <td><code class="type">Any</code><code> != </code><code class="type">Any</code></td>
            <td>Inequality</td>
        </tr>
    </table>
</td>
</tr>

<tr>
<td>6</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Bool</code><code> and </code><code class="type">Bool</code></td>
            <td style="width:12em">Logical AND</td>
        </tr>
    </table>
</td>
</tr>

<tr>
<td>7</td><td>
    <table>
        <tr>
            <td style="width:9em"><code class="type">Bool</code><code> or </code><code class="type">Bool</code></td>
            <td style="width:12em">Logical OR</td>
        </tr>
    </table>
</td>
</tr>

</table>
