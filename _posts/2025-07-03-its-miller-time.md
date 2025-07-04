---
title: "It's Miller (mlr) time!"
date: 2025-07-03
---
## Introduction
Linux provides a powerful environment for data parsing and analysis.
Command-line tools like 'awk', 'sed', and 'jq' are especially useful for
transforming raw data into more structured, readable formats, making it
easier to process and consume.

Today, I want to highlight one of my favorite tools for working with
data formats like CSV and JSON. According to its GitHub repository, it's
described as:

<https://github.com/johnkerl/miller>
```
Miller is like awk, sed, cut, join, and sort for data formats such as CSV, TSV, JSON, JSON Lines, and positionally-indexed.
```

Miller is a powerful tool that simplifies data analysis with its
intuitive domain-specific language (DSL). Be sure to check out the
excellent documentation -- especially the *Getting Started* section for a
quick and helpful introduction.

## Example
Miller's documentation already provides excellent examples, so I'll
simply share a few ways I might use the tool.

### Sample JSON data:
```sh
$ curl -s https://microsoftedge.github.io/Demos/json-dummy-data/64KB.json | head -20
```
```
[
  {
    "name": "Adeel Solangi",
    "language": "Sindhi",
    "id": "V59OF92YF627HFY0",
    "bio": "Donec lobortis eleifend condimentum. Cras dictum dolor lacinia lectus vehicula rutrum. Maecenas quis nisi nunc. Nam tristique feugiat est vitae mollis. Maecenas quis nisi nunc.",
    "version": 6.1
  },
  {
    "name": "Afzal Ghaffar",
    "language": "Sindhi",
    "id": "ENTOCR13RSCLZ6KU",
    "bio": "Aliquam sollicitudin ante ligula, eget malesuada nibh efficitur et. Pellentesque massa sem, scelerisque sit amet odio id, cursus tempor urna. Etiam congue dignissim volutpat. Vestibulum pharetra libero et velit gravida euismod.",
    "version": 1.88
  },
  {
    "name": "Aamir Solangi",
    "language": "Sindhi",
    "id": "IAKPO3R4761JDRVG",
    "bio": "Vestibulum pharetra libero et velit gravida euismod. Quisque mauris ligula, efficitur porttitor sodales ac, lacinia non ex. Fusce eu ultrices elit, vel posuere neque.",
```

### Transform `JSON` -> `CSV`
```sh
$ curl -s https://microsoftedge.github.io/Demos/json-dummy-data/64KB.json \
    | mlr --j2c cut -x -f bio then head
```
```
name,language,id,version
Adeel Solangi,Sindhi,V59OF92YF627HFY0,6.1
Afzal Ghaffar,Sindhi,ENTOCR13RSCLZ6KU,1.88
Aamir Solangi,Sindhi,IAKPO3R4761JDRVG,7.27
Abla Dilmurat,Uyghur,5ZVOEPMJUI4MB4EN,2.53
Adil Eli,Uyghur,6VTI8X6LL0MMPJCC,6.49
Adile Qadir,Uyghur,F2KEU5L7EHYSYFTT,1.9
Abdukerim Ibrahim,Uyghur,LO6DVTZLRK68528I,5.9
Adil Abro,Sindhi,LJRIULRNJFCNZJAJ,9.32
Afonso Vilarchán,Galician,JMCL0CXNXHPL1GBC,5.21
Mark Schembri,Maltese,KU4T500C830697CW,3.17
```

Where
* `--j2c`: convert JSON to CSV
* `cut -x -f bio`: cut out the *bio* field
* `then head`: display the first 10 lines.

### Count each language
```sh
$ curl -s https://microsoftedge.github.io/Demos/json-dummy-data/64KB.json \
    | mlr --j2p count -g language then sort -nr count
```
```
language         count
Hindi            41
Uyghur           20
Maltese          20
Sesotho sa Leboa 20
Sindhi           19
Galician         19
Icelandic        16
Setswana         16
Bosnian          14
isiZulu          12
```

Where
* `--j2p`: JSON to pretty-print
* `count -g language`: count by grouping by *language* field
* `sort -nr count`: reverse sort by *count* field

## Statistics
```sh
$ curl -s https://microsoftedge.github.io/Demos/json-dummy-data/64KB.json \
    | mlr --j2p --barred stats1 -a min,max,mean,p95 -f version
```
```
+-------------+-------------+-------------------+-------------+
| version_min | version_max | version_mean      | version_p95 |
+-------------+-------------+-------------------+-------------+
| 1.01        | 9.99        | 5.605837563451777 | 9.41        |
+-------------+-------------+-------------------+-------------+
```
Where
* `--barred`: draw borders
* `stats1 -a min,max,mean,p95 -f version`: compute *min,max,mean,p95* on field *version*

The result is, of course, meaningless, but I wanted to demonstrate the
use of the `stats1` command.
