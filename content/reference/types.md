+++
title = "Types"
template = "page.html"
weight = 2
+++

In *matchbox*, each variable has a type, and each function has a type signature, only accepting arguments of the expected types. This allows *matchbox* to catch errors when the program starts, and offer helpful error messages. Most programming languages have types, although not all languages enforce them.

Generally, *matchbox* users don't need to worry about learning the types. However, understanding them may help you interpret error messages and put together more complex scripts.

---

<div class="function_block">

## Boolean <code class="type">Bool</code>

A Boolean value. Either `true` or `false`.

**Example**

```matchbox
a = true
b = false
# c will be true
c = a or b
# d will be false
d = a and b


if a => {
    # this will get printed
    stdout!('a was true')
}

if d and c => {
    # this will not get printed!
    stdout!('d and c were both true')
}

```

</div>

---

<div class="function_block">

## Numeric <code class="type">Num</code>

A numeric value. Can be negative, can include a decimal component. Represented internally as a 32-bit floating point number.

**Example**

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

## String <code class="type">Str</code>

A string value, consisting of a sequence of ASCII characters. May represent a nucleotide sequence, or more generally any string data.

Strings must be constructed using single quotes; for example, `'hello world'`. Values can be inserted into strings using curly braces `{}`.

**Example**

```matchbox
message = 'read named {read.id} is {read.seq.len()} bases'

stdout!(message)
```

</div>

---

<div class="function_block">

## List <code class="type">[]</code>

A list of values all belonging to the same type.

Lists can be iterated over when pattern matching, useful when working with barcodes.

**Example**

```matchbox
# has type [Num]
list1 = [10, 2, 3]

# a function, with type (Num) -> Str
f = (n: Num) => n.to_str()

# after mapping the function f over list1,
# list2 has type [Str]
list2 = list1.map(f)
```

```matchbox
# a list of records derived from the CSV header
# in this case, with type [{ name: Str, seq: Str }]
refs = csv('references.csv')

# within a pattern, you can iterate
# over all the values in a list
if read is [_ r.seq _] for r in refs =>
    r.name |> stdout()
```

</div>

---

<div class="function_block">

## Record <code class="type">{}</code>

A set of fields, each of which has a name and stores a value. Used for grouping related data together. Fields can be accessed using a dot.

```matchbox
# creating a record, assigning values to some fields
rec = {
    primer = AAGTCGATGCTAGTG,
    output = 'out.fq',
}

# accessing a field of a record with a dot
if read is [_ rec.primer _] => read.out!(rec.output)
```

<!-- A record type <code class="type">{ name: Str, age: Num }</code> has only two fields: <code>name</code> which is a <code class="type">Str</code>, and `age` which is a <code class="type">Num</code>.

A record type with a double-dot <code class="type">{ name: Str, age: Num ..}</code> has *at least* the fields `name` and `age`, but may also have more fields; it is a less restrictive type. -->

<!-- ```matchbox
tag = (a: { A | desc: Str }) -> { A | desc: Str }

combine = (a: {A | }, b: {B | }) -> {A + B | }

rename = (rec: { name: Str }) -> { name: Str }

duplicate = (rec: { R }) -> { fst: R, snd: R } =>
    { fst = rec, snd = rec }
``` -->

<!-- The universal record type <code class="type">{..}</code> may possess any fields, whereas the empty record type <code class="type">{}</code> has no fields. -->

</div>

---

<div class="function_block">

## Read <code class="type">Read</code>

Reads are a special kind of record to represent a sequencing read.

Reads must contain a `seq` field, and they can be sliced, reverse-complemented and pattern-matched.

Different input formats will produce different kinds of <code class="type">Read</code>. 


</div>

---

# Advanced types

<div class="function_block">

## Function <code class="type">(..) -> ..</code>

A function, which can be applied to a sequence of arguments to return a value. Each function expects arguments of a particular type, and guarantees a return value of a particular type.

A function of type <code class="type">(Num, Num) -> Str</code> takes two arguments of type <code class="type">Num</code> and returns a value of type <code class="type">Str</code>.

An example of such a function is <br>`(n1: Num, n2: Num) => '{n1} and {n2}'`.

**Example**

```matchbox
# function of type (Num, Num) -> Str
#   note that you must declare the argument types,
#   but the return type is inferred
f = (n1: Num, n2: Num) => '{n1} and {n2}'

# function of type (Num, Num) -> Num
g = (n1: Num, n2: Num) => n1 * n2

# function of type ({ seq: Str ..}) -> Num
h = (read: { seq: Str ..}) => read.seq.len()

# function of type ((Num, Num) -> Str, Num) -> Str
#   this function takes in a function fun,
#   as well as a numeric n,
#   then applies fun to n and returns the value!
i = (fun: (Num, Num) -> Str, n: Num) => fun(n, n)

# result has type Str
# evaluates to '10 and 10'
result = i(f, 10)
```

</div>


---

<div class="function_block">

## Any <code class="type">Any</code>

A value of any type. Concrete values will always have a more specific type than <code class="type">Any</code>; it merely exists so that some functions can have sufficiently generic type signatures. For example, `to_str` can convert any value to a <code class="type">Str</code> regardless of its value, so its type signature is <code class="type">(Any) -> Str</code>.

</div>

---

<div class="function_block">

## Type <code class="type">Type</code>

A value which is itself a type. All instances of the above types (e.g. <code class="type">Str</code>, <code class="type">{ age: Num }</code>, <code class="type">(Bool, Bool) -> Bool</code>) are themselves values of this type. Hence, you can create type aliases.

```matchbox
# aliasing a list of Num type as NumList
NumList = [Num]
```

Users probably shouldn't worry about this! But treating types as values is helpful in writing functions whose types depend on the evaluation of other functions.

For example, the function `csv_ty` takes a filename, opens it as a CSV, and returns a record type with each of the CSV's columns as a <code class="type">Str</code> field. Therefore, `csv_ty` has the type <code class="type">(Str) -> Type</code>.

```matchbox
# header_ty has type Type
# and its value is { first_name: Str, last_name: Str }
# (because those are the headers in this particular CSV)
header_ty = csv_ty('friends.csv')
```

The `csv` function loads a CSV and produces a list of records, where the type of each record depends on executing `csv_ty` on the file. Hence, the type of `csv` is <code class="type">[csv_ty(filename)]</code> which evaluates to a concrete type in the presence of a specific filename.

```matchbox
# rows has type [{ first_name: Str, last_name: Str }]
# (the result from evaluating csv_ty on this filename,
#  wrapped in a list)
rows = csv('friends.csv')

# this is an error, because we know each row
# does NOT include a 'seq' field
if read is [_ row.seq _] for row in rows =>
    read |> file('trimmed.fq')
```

<!-- ```matchbox
# NumPair is declared as an alias
# for a particular record type
NumPair = { first: Num, second: Num }

# now, we can define functions which operate on NumPair
get_first = (np: NumPair) => np.first
get_second = (np: NumPair) => np.second
```

```matchbox
# Pair is a function which takes a type
# and returns a record type
Pair = (T: Type) => { first: T, second: T }

# now, we can define functions
# which operate generically on Pair
new_pair = (T: Type) => (first: T, second: T) => { first: T, second: T }
get_first = (T: Type) => (p: Pair(T)) => p.first
get_second = (T: Type) => (p: Pair(T)) => p.second

# NumPair is one specific instance of Pair
NumPair = Pair(Num)

np = new_pair(Num, 10, 12)
first = get_first(Num, np)
``` -->



</div>
