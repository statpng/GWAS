# PLINK Tutorial
PLINK is a popular command-line tool for analyzing genetic data, particularly in genome-wide association studies (GWAS). In this tutorial, we will cover some basic commands and examples to help you get started with PLINK.


## 1. Download and install PLINK:
You can download the latest version of PLINK from the official website (https://www.cog-genomics.org/plink/). Once downloaded, unzip the file and place the PLINK executable in a directory that is included in your system PATH.


## 2. Prepare the data:
Before using PLINK, you need to prepare the genetic data in the PLINK format.

```(1) {PED, MAP}  or  (2) {BIM, BED, FAM}```

  - .ped: PLINK/MERLIN/Haploview text pedigree + genotype (ATGC) table (FID | IID | PID | MID | Sex | P | rs1 | rs2 | rs3)
  - .map: PLINK text fileset variant information file (Chr | SNP | GD | BPP)
  - .bim[^a]: PLINK extended MAP file (Chr | SNP | GD | BPP | Allele1 | Allele2)
  - .bed[^b]: PLINK binary biallelic genotype table (binary version of the SNP information of the .ped file)
  - .fam[^c]: PLINK sample information file (FID | IID | PID | MID | Sex | P)

Additionally, 
  - pheno.txt: phenotype information (FID | IID | P1 | P2 | P3 | ...)
  - covar.txt[^d]: covariates information (FID | IID | C1 | C2 | C3 | ...)

[^a]: biallelic 변이 ID, 위치, Alleles 정보. [a] GD: “0”으로 세팅하는게 안전;  [b] BPP 값이 (-)인 경우, PLINK에서 해당 변이를 무시함;  [c] Allele: biallelic 변이들 정보 (주로 Minor, Major 순서);  [d] Chr: X chromosome (X or 23), Y chromosome (Y or 24), Pseudo-autosomal region of X (XY or 25), Mitochondrial (MT or 26)
[^b]: (variants by sample) compressed matrix. [a] .binary >> read.bed() in r 이용) >> Diploid 유전체에서 각 행렬의 원소는 “A2 (Ref Allele)의 개수” (0, 1, 2)를 나타낸다.
[^c]: [a] FID: 없으면 0;  [b] Sex: 1=male, 2=female, 0=unknown;  [c] Phenotype: 1=control, 2=case, 0 or -9 or non-numeric = missing, numeric value = quantitative trait.
[^d]:  By default, the main phenotype is set to missing if any covariate is missing; you can disable this with the 'keep-pheno-on-missing-cov' modifier.

Data can be transformed into other formats using the table below:
| Format | Generate | Input option |
| ------ | :------: | :------: |
| {PED, MAP} | --recode | --file |
| {BED, BIM, FAM} | --make-bed | --bfile |
| {TPED, TFAM} | --recode --transpose | --tfile |
| {LGEN, MAP, FAM} | --recode-lgen | --lfile |
> For example, ```plink --file <filename>``` for {PED, MAP} or ```plink --bfile <filename>``` for {BED, BIM, FAM}.
> PLINK also supports conversion from VCF format as ```plink --vcf <filename.vcf.gz> --make-bed```



## 3. Basic operations:
Once the data is ready, you can start using PLINK. Here are some of the basic operations that you can perform:

- Summary Statistics
This command will compute basic statistics (such as allele frequencies and missing data rates) for the dataset in the input files.
  - ```plink --file <filename> --freq```
  - ```plink --file <filename> --missing ``` checks for missing data.

- Quality Control (QC)
This command will perform quality control (QC) on the dataset by removing SNPs and individuals that do not meet certain criteria. In this example, SNPs with a missing genotype rate higher than 5% (--geno 0.05), a minor allele frequency lower than 1% (--maf 0.01), and a significant deviation from Hardy-Weinberg equilibrium (--hwe 0.00001) will be removed. Individuals with a missing genotype rate higher than 10% (--mind 0.1) will also be removed. The filtered dataset will be saved in the output files.
  - ``` plink --file <filename> --geno <maximum-per-variant> --mind <maximum-per-sample> ```
  filters out SNPs and samples with a missing genotype rate higher than <maximum-per-variant> and <maximum-per-sample>, respectively.
  
  - ``` plink --file <filename> --maf <minimum-freq> --max-maf <maximum-freq> ``` 
  filters out SNPs whose minor allele frequencies fall outside the range of ```<minimum-freq>``` to ```<maximum-freq>```.
  
  - ``` plink --file <filename> --hwe <p-value> ``` 
  filters out all variants which have Hardy-Weinberg equilibrium exact test p-value below the provided threshold.

  - ```plink --file <filename> --remove <bad_samples.txt>``` 
  remove samples.
  
  - ```plink --file <filename> --indep-pairwise <window size> <step size> <r2 threshold>``` 
  is an LD-based pruning[^1] to select a SNP subset in approximate Linkage Disequilibrium (LD)
    - ```.prune.in```: a pruned subset of marker IDs that are in approximate linkage equilibrium with each other
    - ```.prune.out```: the IDs of all excluded variants.
[^1]: They are currently based on correlations between genotype allele counts; phase is not considered. (Results may be slightly different from PLINK 1.07, due to a minor bugfix in the r2 computation when missing data is present, and more systematic handling of multicollinearity.)
  

  
## 4. Association analysis
```plink --bfile <filename> --pheno <pheno.txt> --covar <covar.txt> --covar-name <cov1>,<cov2> --linear --out <output>```
This command will perform a linear regression analysis of each SNP on the phenotype (stored in the pheno.txt file) while controlling for covariates (stored in the covar.txt file). Instead of ```--linear```, the following options can be either replaced or used together:
  - ```--fisher```
  - ```--model```
  - ```--linear```
  - ```--logistic```
  
  
## 5. Advanced operations:
PLINK also supports several advanced operations such as data imputation, LD-based pruning, and haplotype analysis. Here are some examples:
  - ```plink --bfile <input> --pca --out <output>``` performs a principal component analysis (PCA) on the genetic data to identify population structure.
- ```plink --file <filename> --geno <threshold> --impute``` imputes missing genotypes using the reference panel.

- Haplotype analysis[^2]: To perform haplotype analysis, use the command ```plink --file <filename> --hap <output filename>```
[^2]: PLINK 1.7 version
  
  
  

## 6. One-line PLINK command for GWAS
```plink --file <input> --geno 0.05 --maf 0.01 --hwe 0.00001 --mind 0.1 --pca --covar <covar.txt> --pheno <pheno.txt> --pheno-name <phenotype_name> --assoc --adjust --out <output>```

  - --assoc / --fisher / --model / --linear / --logistic

  
## 7. Other useful commands

> --make-bed, --recode, --flip-scan, --merge-list, --write-snplist, --list-duplicate-vars, --freqx, --missing, --test-mishap, --hardy, --mendel, --ibc, --impute-sex, --indep-pairphase, --r2, --show-tags, --blocks, --distance, --genome, --homozyg, --make-rel, --make-grm-gz, --rel-cutoff, --cluster, --pca, --neighbour, --ibs-test, --regress-distance, --model, --bd, --gxe, --logistic, --dosage, --lasso, --test-missing, --make-perm-pheno, --unrelated-heritability, --tdt, --dfam, --qfam, --tucc, --annotate, --clump, --gene-report, --meta-analysis, --epistasis, --fast-epistasis, and --score

  
  
## 8. References
- Manual: https://zzz.bwh.harvard.edu/plink/data.shtml
- Tutorial 1: https://www.bioinf.wits.ac.za/courses/sahgp/plink-tut-jul14.pdf
- Tutorial 2: https://genomicsbootcamp.github.io/book/principal-component-analysis-pca.html#run-a-pca-in-r 
- Lecture: http://faculty.washington.edu/tathornt/SISG2021.html
- GRS: https://mopipe.tistory.com/129
