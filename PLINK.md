# PLINK


## Table of Contents
- [Introduction](#introduction)
- [Data Preparation](#data-preparation)
- [Quality Control](#quality-control)
- [Association Testing](#association-testing)
- [Multiple Testing Correction](#multiple-testing-correction)
- [Visualization](#visualization)


## Introduction
- Overview of the PLINK software package and its use in genome-wide association studies (GWAS) and genetic analysis tasks.

## Data Preparation
- Converting raw genotype data into PLINK format using `plink --make-bed`.
- Input file formats: binary PED file (`*.bed`), PED file (`*.ped`), and MAP file (`*.map`).

## Quality Control
- Assessing and filtering input data for missing data, sample mix-ups, and population stratification using `plink --missing`, `plink --check-sex`, and `plink --cluster`.
- Removing samples or variants that fail quality control using `plink --remove` or `plink --exclude`.

## Association Testing
- Performing basic chi-squared tests using `plink --assoc`.
- Performing logistic regression using `plink --logistic`.
- Performing linear regression using `plink --linear`.
- Testing each variant for association with the phenotype of interest.

## Multiple Testing Correction
- Performing permutation testing using `plink --perm`.
- Applying Bonferroni correction using `plink --adjust`.
- Correcting for multiple testing to adjust the p-values of the association tests.

## Visualization
- Outputting association test results in various formats such as `*.assoc`, `*.qassoc`, and `*.logistic`.
- Visualizing the results using software such as R or Python.

Note: Each section can be expanded to provide more detailed information about the PLINK workflow.
