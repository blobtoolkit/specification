# BlobDir specification

<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1. Overview](#1-overview)
- [2. Directory structure](#2-directory-structure)
	- [2.1 Minimal example](#21-minimal-example)
	- [2.2 Typical example](#22-typical-example)
	- [2.3 Summary and descriptive files](#23-summary-and-descriptive-files)
	- [2.4 Required files](#24-required-files)
- [3. Metadata](#3-metadata)
	- [3.1 File structure](#31-file-structure)
- [X. Validator](#x-validator)
	- [X.1 Json schema validation](#x1-json-schema-validation)
	- [X.2 Additional validation](#x2-additional-validation)

<!-- /TOC -->

## 1. Overview
`BlobDir` is the BlobToolKit file format, currently written by [BlobTools2](https://github.com/blobtoolkit/blobtools2) and parsed by BlobTools2 and the [BlobToolKit Viewer](https://github.com/blobtoolkit/viewer).

## 2. Directory structure
A `BlobDir` is a standard filesystem directory that may optionally be `tar`ed and `gzip`ed. It should be named to match the primary identifier for the dataset.

### 2.1 Minimal example
A minimal `BlobDir` must contain at least two `JSON` format files: `meta.json` containing dataset metadata and `identifiers.json` containing the primary identifiers for the constituent records.

```
DatasetID
+- meta.json
+- identifiers.json
```

### 2.2 Typical example
Each additional field in the dataset must have a corresponding `JSON` format file in the `BlobDir`. In a typical BlobDir these will contain information derived from the constituent sequences of a genome assembly (GC content, contig/scaffold/chromosome lengths and number of Ns), details of read-mapping, taxonomic inference based on sequence-similarity searches and BUSCO results.

```
DatasetID
+- meta.json
+- identifiers.json
+- gc.json
+- length.json
+- ncount.json
+- {LIBRARYNAME}_cov.json
+- {LIBRARYNAME}_read_cov.json
+- {TAXRULE}_{RANK}.json
+- {TAXRULE}_{RANK}_cindex.json
+- {TAXRULE}_{RANK}_positions.json
+- {TAXRULE}_{RANK}_score.json
+- {LINEAGE}_busco.json
```

### 2.3 Summary and descriptive files
A `BlobDir` may contain additional files to provide a human-readable summary and/or description of the dataset in `TSV` (`summary.tsv`) and `MARKDOWN` (`description.md`) formats.  

```
DatasetID
+- meta.json
+- identifiers.json
...
+- summary.tsv
+- description.md
```

### 2.4 Required files
As noted in section [2.1](#21-minimal-example), a valid `BlobDir` can be created with only two files, `meta.json` and `identifiers.json`. This allows a `BlobDir` to be generated iteratively, with new fields added as the data become available. For specific uses of a `BlobDir`, additional files may be required, for example the [BlobToolKit Viewer](https://github.com/blobtoolkit/viewer) requires at least a `gc.json`, a `length.json` and a `{TAXRULE}_{RANK}.json` file to produce meaningful plots.

## 3. Metadata
A `meta.json` file is required and must contain metadata describing the dataset and its constituent fields in `JSON` format.

### 3.1 File structure
The top level of the `meta.json` file is a `JSON` object with


## X. Validator

### X.1 Json schema validation
 - meta.json
   - convert meta to run schema validation on meta fields
 - field data validation
   - generate schema for array/multiarray
   - set range as max and min for values
   - set minItems and maxItems to get array length

### X.2 Additional validation
 - check data files match fields
 - check length of value arrays (minItems, maxItems)
 - check taxids/taxa are valid?
