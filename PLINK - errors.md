## Errors


### "Warning: 775 het. haploid genotypes present (see chr_af.hh ); many commands treat these as missing."

- `.hh` 파일 확인용 코드

``` system("cat ./data/merge0422.hh | awk '{print $3}'", intern=TRUE) %>% table ```

- 아직 무슨 문제인지 이해가 안됨. 

> CHR을 1번부터 22번으로 제한하면 해결가능.

```system("~/plink/plink --ped ./data/merge0422_ped-map.ped --map ./data/merge0422_ped-map.map --recode vcf --chr 1-22 --out ./data/merge0422_vcf_no-sex")```



### "At least one VCF allele code violates the official specification; other tools may not accept the file. (Valid codes must either start with a '<', only contain characters in {A,C,G,T,N,a,c,g,t,n}, be an isolated '*', or represent a breakend.)"

- ACGT 이외의 다른 문자가 포함되어 나오는 문제. 
> `--snps-only just-acgt`을 사용하여 해결 가능.

``` system("~/plink/plink --ped ./data/merge0422_ped-map.ped --map ./data/merge0422_ped-map.map --recode vcf --chr 1-22 --snps-only just-acgt --out ./data/merge0422_vcf_no-sex+just-acgt") ```


### "Warning: Underscore(s) present in sample IDs."

- 샘플 ID에 underscore ("_")가 있어서 나타나는 문제 (나중에 PLINK에서 "_"를 이용하여 column을 나누기 때문). <br>
> "_"를 제거해주면 해결 가능.

- R에서 해결해줘도 되지만, shell에서 해결하는 코드:

```system("sed 's/_//g' ./data/merge0422_ped-map.ped > ./data/merge0422_ped-map_rm-underscore.ped")``` <br>
```system("sed 's/_//1' ./data/merge0422.pheno > ./data/merge0422_rm-underscore0.pheno")```

```system("sed 's/_//1' ./data/merge0422.pheno > ./data/merge0422_rm-underscore0.pheno")``` <br>
```system("sed 's/_//1' ./data/merge0422_rm-underscore0.pheno > ./data/merge0422_rm-underscore.pheno")```

```system("head -n 3 ./data/merge0422_rm-underscore.pheno")``` <br>
```system("head -n 3 ./data/merge0422_ped-map_rm-underscore.ped | awk '{print $1, $2, $3, $4}'")```

  * `sed s/before/after/`: before에서 after로 바꾼다
  * `/g`: 모든 "_"를 ""로 바꾼다
  * `/1`: 첫번째 열에서 "_"를 ""로 바꾼다



### Michigan Imputation Server에서 VCF파일 에러 났을때

- (1) 각 CHR별로 쪼개야하고, (2) vcf.gz 포맷으로 만들어야함.
> (1) `--chr` 옵션을 이용해서 CHR 나누기, (2) bcftools를 이용해서 vcf를 vcf.gz로 변환.

```
for( i in 1:22 ){
  system(paste0("~/plink/plink --ped ./data/merge0422_ped-map_rm-underscore.ped --map ./data/merge0422_ped-map.map --recode vcf --chr ", i, " --snps-only just-acgt --out ./data/merge0422_vcf_no-sex_just-acgt_rm-underscore_chr",i))
  system(paste0("bcftools sort ./data/merge0422_vcf_no-sex_just-acgt_rm-underscore_chr",i,".vcf -Oz -o ./data/merge0422_vcf_no-sex_just-acgt_rm-underscore_chr",i,".vcf.gz"))
}
```
