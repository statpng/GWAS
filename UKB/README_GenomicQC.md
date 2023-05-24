
```
#!/bin/bash

# Download Imputation Score

## Note that you should have a directory "./QC/ukb_imp_mfi".

#for i in {1..22} X XY
#do
#    wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_mfi_chr${i}_v3.txt -P ./QC/ukb_imp_mfi
#done

# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_snp_qc.txt
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_snp_bim.tar
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_imp_bgi.tgz
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_imp_mfi.tgz
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_hap_bgi.tgz
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_snp_posterior.tar
# wget https://biobank.ctsu.ox.ac.uk/ukb/ukb/auxdata/ukb_snp_posterior.batch



# 1. Imputation Score
#for i in {1..22}
## for i in 1 10 12 {14..19}
#do
#  awk -v chr=$i 'BEGIN {FS="\t"; OFS="\t"} NF==8 && $8 != "NA" { print chr,$0,chr":"$3"_"$4"_"$5 }' ./QC/ukb_imp_mfi/ukb_mfi_chr${i}_v3.tsv
#done > ./QC/ukb_mfi_all_v3.tsv

# In R
# path <- "/Volumes/T7/2.UKB/Genetics/QC/ukb_imp_mfi"
# out <- map(list.files(path, full.names = TRUE), ~ data.table::fread(.x) %>% filter( !is.na(V8) ))
# data.table::fwrite( out %>% dplyr::bind_rows(), "/Volumes/T7/2.UKB/Genetics/QC/ukb_mfi_all_v3.tsv")







## 2. Format Convergion
for i in {1..22}
# for i in 1 10 12 {14..19}
do
  echo "Running for chromosome $i"

  ~/plink2 --bgen ./bgen/ukb22828_c${i}_b0_v3.bgen ref-first \
       --hard-call-threshold 0.1 \
       --sample ./sample/ukb22828_c${i}_b0_v3_s4871*.sample \
       --memory 20000 \
       --set-all-var-ids @:\#_\$r_\$a \
       --new-id-max-allele-len 1000 \
       --freq \
       --make-pgen \
       --out ukb22828_c${i}_b0_v3





## 3. SNP QC with high heterozygosity
  awk '/^[^#]/ { if( $5>0.4 && $5<0.6 && ( ($3=="A" && $4=="T") || ($4=="T" && $3=="A") || ($3=="C" && $4=="G") || ($4=="G" && $3=="C") ) ) { print $0 }}' \
ukb22828_c${i}_b0_v3.afreq > exclrsIDs_c${i}_ambiguous.txt




## 4. SNP QC: Imp. score + MAF + Used_in_PCA (call rate and etc.)

  ~/plink2  --pfile ukb22828_c${i}_b0_v3 \
      --memory 20000 \
      --exclude exclrsIDs_c${i}_ambiguous.txt \
      --extract-col-cond ./QC/ukb_mfi_all_v3.tsv 9 10 \
	  --extract-col-cond-min 0.4 \
      --maf 0.005 \
      --write-snplist \
      --make-pgen \
      --out ukb22828_c${i}_b0_v3_snpQC


## 5. Sample QC: refer to png.ukb::png.ukb_QC
  ~/plink2  --pfile ukb22828_c${i}_b0_v3 \
      --memory 20000 \
      --extract ukb22828_c${i}_b0_v3_snpQC.snplist \
      --keep-fam ./QC/ukb_data_genetics_AfterQC_eid.txt \
      --write-samples \
      --out ukb22828_c${i}_b0_v3_sampleQC




done

echo "All chromosomes completed"




# cd E:\UKB

for chr in {1..22}
do
    filename=ukb22828_c${chr}_b0_v3
~/plink2 --pfile ${filename} \
	--memory 20000 \
	--extract ${filename}_snpQC.snplist \
	--keep ${filename}_sampleQC.id \
	--make-bed \
	--out /mnt/g/UKB_QC_BBF/${filename}
done
	


```
