# PLINK Tutorial
PLINK is a popular command-line tool for analyzing genetic data, particularly in genome-wide association studies (GWAS). In this tutorial, we will cover some basic commands and examples to help you get started with Plink.


1. Download and install plink:
You can download the latest version of plink from the official website (https://www.cog-genomics.org/plink/). Once downloaded, unzip the file and place the plink executable in a directory that is included in your system PATH.


2. Prepare the data:
Before using plink, you need to prepare the genetic data in the PLINK format.

```(1) {PED, MAP};  (2) {BIM, BED, FAM}```

- .ped: PLINK/MERLIN/Haploview text pedigree + genotype table (ACGT)
- .map: PLINK text fileset variant information file
- .bim: PLINK extended MAP file
- .bed: PLINK binary biallelic genotype table
- .fam: PLINK sample information file

Data can be transformed into other formats using the table below:
| format | Generate | Input option |
| ------ | :------: | :------: |
| {PED, MAP} | --recode | --file |
| {BED, BIM, FAM} | --make-bed | --bfile |
| {TPED, TFAM} | --recode --transpose | --tfile |
| {LGEN, MAP, FAM} | --recode-lgen | --lfile |
> PLINK also supports conversion from VCF format as ```plink --vcf <filename.vcf.gz> --make-bed```

3. Basic operations:
Once the data is ready, you can start using PLINK. Here are some of the basic operations that you can perform:
- Summary Statistics
This command will compute basic statistics (such as allele frequencies and missing data rates) for the dataset in the input files.
  - Show basic statistics: To get basic statistics on the dataset, use the command ```plink --file <filename> --summary ```
  - ```plink --file <filename> --freq```
  - Check for missing data: To check for missing data, use the command ``` plink --file <filename> --missing ```
- Quality Control (QC)
This command will perform quality control (QC) on the dataset by removing SNPs and individuals that do not meet certain criteria. In this example, SNPs with a missing genotype rate higher than 5% (--geno 0.05), a minor allele frequency lower than 1% (--maf 0.01), and a significant deviation from Hardy-Weinberg equilibrium (--hwe 0.00001) will be removed. Individuals with a missing genotype rate higher than 10% (--mind 0.1) will also be removed. The filtered dataset will be saved in the output files.
  - Filter the data: To filter the data based on various criteria such as allele frequency, genotyping rate, etc., use the command ``` plink --file <filename> --maf <threshold> --geno <threshold> ```
  - Perform association tests: To perform association tests such as logistic regression, use the command "plink --file <filename> --logistic".
  - Conduct genome-wide association study (GWAS): To perform a GWAS, use the command ```plink --file <filename> --assoc```
  - Conduct principal component analysis (PCA): To perform PCA on the dataset, use the command ```plink --file <filename> --pca```
  - Remove samples: ```plink --bfile <filename> --remove <bad_samples.txt>```
  - LD pruning: selecting a SNP subset in approximate Linkage Disequilibrium (LD)
  ```plink --bfile input --indep-pairwise 50 5 0.2 --out output```

  
4. Association analysis
```plink --bfile <filename> --linear --pheno pheno.txt --covar covar.txt --covar-name <cov1>,<cov2> --out <output>```
This command will perform a linear regression analysis of each SNP on the phenotype (stored in the pheno.txt file) while controlling for covariates (stored in the covar.txt file). The results will be saved in the output files.

  - --fisher
  - --model
  - --linear
  - --logistic
  
4. Advanced operations:
Plink also supports several advanced operations such as data imputation, LD-based pruning, and haplotype analysis. Here are some examples:
  - PCA: This command will perform a principal component analysis (PCA) on the genetic data to identify population structure. The results will be saved in the output files: ```plink --bfile <input> --pca --out <output>```
- Impute missing genotypes: To impute missing genotypes using the reference panel, use the command ```plink --file <filename> --geno <threshold> --impute```
- LD-based pruning[^1]: To perform LD-based pruning, use the command ```plink --file <filename> --indep-pairwise <window size> <step size> <r2 threshold>```
  - ```.prune.in```: a pruned subset of marker IDs that are in approximate linkage equilibrium with each other
  - ```.prune.out```: the IDs of all excluded variants.
[^1]: They are currently based on correlations between genotype allele counts; phase is not considered. (Results may be slightly different from PLINK 1.07, due to a minor bugfix in the r2 computation when missing data is present, and more systematic handling of multicollinearity.)
  
  
- Haplotype analysis: To perform haplotype analysis, use the command ```plink --file <filename> --hap <output filename>```

5. Output formats:
Plink can output results in several formats such as text, binary, and VCF. To specify the output format, use the ```--out <output filename>``` flag followed by the desired format extension.
These are just a few examples of what you can do with plink. For more information on plink and its commands, refer to the official documentation (https://www.cog-genomics.org/plink/1.9/).

  
  
  

```plink --bfile <input> --geno 0.05 --maf 0.01 --hwe 0.00001 --mind 0.1 --pca --covar ./data/covar.txt --pheno <input.pheno> --pheno-name <pheno_name> --linear --adjust --out <output>```
  

  

  
### Other useful commands

> --make-bed, --recode, --flip-scan, --merge-list, --write-snplist, --list-duplicate-vars, --freqx, --missing, --test-mishap, --hardy, --mendel, --ibc, --impute-sex, --indep-pairphase, --r2, --show-tags, --blocks, --distance, --genome, --homozyg, --make-rel, --make-grm-gz, --rel-cutoff, --cluster, --pca, --neighbour, --ibs-test, --regress-distance, --model, --bd, --gxe, --logistic, --dosage, --lasso, --test-missing, --make-perm-pheno, --unrelated-heritability, --tdt, --dfam, --qfam, --tucc, --annotate, --clump, --gene-report, --meta-analysis, --epistasis, --fast-epistasis, and --score
