# BlobDir specification

<!-- TOC depthFrom:2 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1. Overview](#1-overview)
- [2. Directory structure](#2-directory-structure)
	- [2.1 Minimal example](#21-minimal-example)
	- [2.2 Typical example](#22-typical-example)
	- [2.3 Summary and descriptive files](#23-summary-and-descriptive-files)
	- [2.4 Required files](#24-required-files)
- [3. Schema](#3-schema)
	- [3.1 meta](#31-meta)
	- [3.2 fields](#32-fields)
		- [3.2.1 Identifier](#321-identifier)
		- [3.2.2 Variable](#322-variable)
		- [3.2.3 Category](#323-category)
		- [3.2.4 Array](#324-array)
		- [3.2.4 MultiArray](#324-multiarray)

<!-- /TOC -->

## 1. Overview
`BlobDir` is the BlobToolKit file format, currently written by [BlobTools2](https://github.com/blobtoolkit/blobtools2) and parsed by BlobTools2 and the [BlobToolKit Viewer](https://github.com/blobtoolkit/viewer).

More information and tutorials are available at [blobtoolkit.genomehubs.org/specification/](https://blobtoolkit.genomehubs.org/specification/)

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
A `BlobDir` may contain additional files to provide a machine-readable summary and/or human-readable description of the dataset in `JSON` (`summary.json`) and `MARKDOWN` (`description.md`) formats.

```
DatasetID
+- meta.json
+- identifiers.json
...
+- summary.json
+- description.md
```

### 2.4 Required files
As noted in section [2.1](#21-minimal-example), a valid `BlobDir` can be created with only two files, `meta.json` and `identifiers.json`. This allows a `BlobDir` to be generated iteratively, with new fields added as the data become available. For specific uses of a `BlobDir`, additional files may be required, for example the [BlobToolKit Viewer](https://github.com/blobtoolkit/viewer) requires at least a `gc.json`, a `length.json` and a `{TAXRULE}_{RANK}.json` file to produce meaningful plots.

## 3. Schema

A formal [JSON Schema](https://json-schema.org) definition of the `BlobDir` file structure definition is provided in the `schema` directory of this repository. This schema is dynamically updated by the by the validator and is divided into the sections described below.

### 3.1 meta
A `meta.json` file containing metadata describing the `BlobDir` and its constituent fields is required. `meta.json` must conform to the metadata schema (`schema/meta.schema.json`) and subschemas for each of the main subsections:

 - assembly (`schema/subschemas/assembly.schema.json`)
 - fields (`schema/subschemas/fields.schema.json`)
 - plot (`schema/subschemas/plot.schema.json`)
 - reads (`schema/subschemas/reads.schema.json`)
 - taxon (`schema/subschemas/taxon.schema.json`)

### 3.2 fields

Fields are represented by `JSON` format files containing one entry per record in the `BlobDir`. Each field must conform to two additional schemas, one representing the metadata and one constraining the accepted values for the given datatype. Valid datatypes are `identifier`, `variable`, `category`, `array`, and `multiarray`. Field schemas are of the form `schema/subschemas/<datatype>.meta.schema.json` and `schema/subschemas/<datatype>.data.schema.json`. During validation, all field schemas are manipulated to ensure the number of entries matches the number of records in the `BlobDir` metadata.

#### 3.2.1 Identifier

Each `BlobDir` must contain one `identifier` field containing an array with one entry per record in the `BlobDir`. Each identifier must be a unique string.

#### 3.2.2 Variable

Variable fields contain one value (integer or float) per record. During validation, all `variable` schemas are manipulated to ensure the maximum and minimum values match the range defined in the `BlobDir` metadata.

#### 3.2.3 Category

Category fields provide a compact means of storing repetitive strings. Each record is represented by an index to an entry in an array of keys. All indexes must have a corresponding entry in the array of keys and all keys must be unique.

#### 3.2.4 Array

Array fields contain two or more values (which may be a mix of `variable` and `category`) per record. A `headers` array specifies the field names.

#### 3.2.4 MultiArray

MultiArray fields contain two or more `array`s per record. These may be used, for example, to list a set of BLAST hits with associated scores and positions.
