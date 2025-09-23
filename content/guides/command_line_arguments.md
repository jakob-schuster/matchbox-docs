+++
title = "Command-line arguments"
template = "page.html"
weight = 6
+++

When writing *matchbox* scripts intended for others to run, or to be embedded in pipelines, it can be useful to make some variables accessible to users on the command line, so they can change them when running your script without having to edit the script itself.

The built-in `args` variable can be used for this purpose. It is a <code class="type">Record</code> value accessible within the *matchbox* script, whose fields come from the command line argument `--args`.

In the following script, a primer is located and 10 bp following it are extracted:

```matchbox
if read matches [_ args.primer bc:|10| _] => count!(bc.seq)
```

To run the script, the `primer` argument must be provided at the command line:

```
matchbox -s my_script.mb --args "primer = AGCTGATGCTGT"
```

We could make the script more flexible by allowing the user to specify the barcode length:

```matchbox
if read matches [_ args.primer bc:|args.barcode_length| _] => 
    count!(bc.seq)
```

When multiple arguments are given, they're separated by commas. 

```
matchbox -s my_script.mb \
    --args "primer = AGCTGATGCTGT, barcode_length = 10"
```
