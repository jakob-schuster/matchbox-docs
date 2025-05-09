+++
title = "Expressions"
template = "page.html"
weight = 3
+++

Expressions are the smallest unit of *matchbox* syntax.


<!-- <table>
    <th>Name</th>
    <th>Syntax</th>
    <th>Description</th>
    <tr>
        <td>Boolean literal</td>
        <td><code>true</code>, <code>false</code></td>
        <td>Matches any number of any nucleotides.</td>
    </tr>
    <tr>
        <td>Numeric literal</td>
        <td>n</td>
        <td>Matches any number of any nucleotides.</td>
    </tr>
    <tr>
        <td>String </td>
        <td>n</td>
        <td>Matches any number of any nucleotides.</td>
    </tr>
</table> -->


---

<div class="function_block">

## Variables

A variable name, defined earlier in the script. 

Variable names can include letters, numbers and underscores. A name must start with a letter or an underscore. Variable names must be unique, and can't be overwritten.

```matchbox
a = 'hello'
# expressions can refer to bound variables
b = a

# variables bound in patterns are accessible 
# inside the body of the branch
if read is [fst:|5| _] => fst.seq.stdout!()

# but NOT outside it! 
fst.seq.len().average!()
```

</div>

---

<div class="function_block">

## Boolean literals

There are only two <code class="type">Bool</code> literals: `true` and `false`.

</div>

---

<div class="function_block">

## Numeric literals

Numeric literals can be negative, and can include a decimal component. 

```matchbox
a = 10000

# b will be -1000
b = a / -10

# c will be -999.99
c = b + 0.01
```

</div>

---

<div class="function_block">

## String literals

Strings must be constructed using single quotes; for example, `'hello world'`. Values can be inserted into strings using curly braces `{}`.

```matchbox
message = 'read named {read.id} is {read.seq.len()} bases'

stdout!(message)
```

</div>

---

<div class="function_block">

## Record literals

Record literals are a set of curly braces, containing a number of fields. Each field has a name and an expression, separated by equals. 

Fields can be accessed using a dot.

```matchbox
# creating a record, assigning values to some fields
rec = {
    primer = AAGTCGATGCTAGTG,
    output = 'out.fq',
}

# accessing a field of a record with a dot
if read is [_ rec.primer _] => read.out!(rec.output)
```
</div>

---

<div class="function_block">

## Function literals

New functions can be defined using function literal syntax. Arguments must be declared with their types, in parentheses, separated by commas. The body of the function comes after an arrow `=>`. The function's return type is inferred from its body.

Variable names can be assigned to functions, just like any other value.

```matchbox
# a function that  numbers
f = (n1: Num, n2: Num) => n1 * 3

# a function which formats the result of f into a Str
g = (n: Num) => '{n} times two equals {f(n)}'
```

Functions can also take optional named arguments. These must come after positional arguments in the function definition, and they have a default value which is an expression. 

```matchbox
print_both = (v1: Str, v2: Str, separator: Str = ' & ') =>
    '{v1}{separator}{v3}'
```

</div>

---

<div class="function_block">

## Function application

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

Some functions take optional named arguments. These must be given after all the mandatory arguments. The optional arguments themselves can then be given in any order.

```matchbox
read.describe(
    { polya = AAAAAAAAAA }, 
    reverse_complement = true
).count!()
```

</div>

---

<div class="function_block">

## Operators

A number of built-in common operators can be used. They are applied infix.

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
    <i>All other expressions</i><br>
    <!-- <i>Function / method calls</i><br>
    <i>Accessing fields of records</i><br> -->
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
            <td><code> -</code>( <code class="type">Str</code> | <code class="type">Read</code> )</td>
            <td>Reverse-complementation</td>
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


</div>