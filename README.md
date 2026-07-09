# CRISPR Spacer Analysis

A lightweight Python pipeline for clustering CRISPR spacer sequences and building comparative presence/absence matrices across bacterial isolates.

This project was developed in the context of a Bachelor thesis on the genomic characterization of *Phytobacter* isolates, with a focus on CRISPR spacer diversity and strain comparison.

## Overview

The pipeline reads CRISPR spacer sequences from a FASTA file, groups similar spacer sequences by Hamming distance, and exports CSV files that can be used for downstream comparative analysis.

It is designed for small to medium spacer datasets where transparent parsing, reproducible clustering rules, and simple tabular outputs are more useful than a large framework.

## Features

- Parses FASTA files containing CRISPR spacer sequences from multiple isolates
- Supports multiple CRISPR arrays per isolate
- Groups similar spacers using Hamming distance
- Uses a default clustering threshold of at most 2 mismatches
- Keeps spacer groups that occur at least twice across the dataset
- Generates a spacer definition table
- Generates an isolate-by-spacer presence/absence matrix
- Provides a simple command-line interface

## Repository Structure

```text
.
├── main.py      # Command-line entry point
├── parser.py    # FASTA parsing and spacer clustering logic
├── process.py   # Pipeline orchestration and CSV export
└── README.md
```

## Requirements

- Python 3.9 or newer recommended

The current implementation uses only the Python standard library. No additional packages are required.

## Input Format

The input file should be a FASTA file where spacer headers include an isolate ID and spacer number:

```fasta
>Isolate1_spacer1
ACGTACCTGAA
>spacer2
CGGCTTACGTA
>Isolate2_spacer1
ACGTACCTGAT
>spacer2
TTAGGCTACCA
```

Header behavior:

- Headers such as `Isolate1_spacer1` start a new isolate entry.
- Subsequent headers such as `spacer2`, `spacer3`, and so on are assigned to the current isolate.
- A repeated `spacer1` within the same isolate starts a new CRISPR array.

## Usage

Run the pipeline from the repository root:

```bash
python main.py path/to/spacers.fasta --output results/spacer_analysis
```

The `--output` argument is used as an output prefix. For example, the command above creates:

```text
results/spacer_analysis.csv
results/spacer_analysis_definition.csv
```

## Outputs

### Presence/Absence Matrix

The main CSV file reports whether each isolate contains a spacer from each retained spacer group.

Example:

| Name/Id  | S001 | S002 | S003 |
|----------|------|------|------|
| Isolate1 | x    | -    | x    |
| Isolate2 | x    | x    | -    |

Values:

- `x`: spacer group is present in the isolate
- `-`: spacer group is absent from the isolate

### Spacer Definition Table

The definition CSV maps each spacer group ID to the sequence or sequences assigned to that group.

Example:

| Id   | Sequence                 |
|------|--------------------------|
| S001 | ACGTACCTGAA; ACGTACCTGAT |
| S002 | CGGCTTACGTA              |
| U001 | TTAGGCTACCA              |

Spacer IDs beginning with `S` represent retained spacer groups. Spacer IDs beginning with `U` represent ungrouped sequences that did not meet the frequency threshold for the main presence/absence matrix.

## Method Summary

1. Parse spacer sequences from FASTA input.
2. Organize spacers by isolate and CRISPR array.
3. Count unique spacer sequences across the full dataset.
4. Cluster sequences of equal length with a Hamming distance of 2 or less.
5. Retain groups whose total frequency is at least 2.
6. Export spacer definitions and isolate-level presence/absence calls.

## Notes

- Sequences with different lengths are not grouped by Hamming distance.
- The clustering threshold is currently defined in `parser.py`.
- CSV output is intentionally simple so results can be opened directly in spreadsheet software or imported into downstream analysis tools.

## Author

**Michèle Pfister**  
MSc Applied Computational Life Sciences  
BSc Biomedical Laboratory Diagnostics
