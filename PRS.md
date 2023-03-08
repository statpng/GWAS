https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwj73vbP9Mb9AhUSPXAKHc6rAV0QFnoECDgQAQ&url=https%3A%2F%2Fwww.kdca.go.kr%2Ffilepath%2FboardDownload.es%3Fbid%3DATT%26list_no%3D718107%26seq%3D1&usg=AOvVaw1VFGajnHPB2YvgOREAJHv8




# Polygenic Risk Score (PRS)

X: SNPs in our data

SumStat: Summary Statistics

RefPanel: Reference Panel

1. SumStat 정보가 있는 경우
   - SumStat을 계산하는데 사용된 RefPanel을 준비함
   - X 중에서 RefPanel에 있는 SNP만 추출함
   - 선별된 X + SumStat + clumping (rsq < 0.1~0.2; 250kb)을 이용해 PRS 계산
2. SumStat 없는 경우
   - RefPanel을 하나 준비함 (why?)
   - X 중에서 RefPanel에 있는 SNP만 추출함
   - 선별된 X에 대해 보유한 데이터로 summary statistics를 계산함
   - 선별된 X + 우리 데이터로 계산한 SumStat + clumping을 이용해 PRS 계산

(1)

- (SumStat 있는 경우) (X=Our SNP, RefPanel=SumStat's RefPanel) >> X 중 RefPanel에 있는 SNP들만 필터링 >> (선별된 SNP, SumStat)
- (SumStat 없는 경우) (X=Our SNP, RefPanel=Appropriate RefPanel) >> X 중 RefPanel에 있는 SNP들만 필터링 >> 보유한 데이터로 회귀분석 실시 후 SumStat 계산 >> (선별된 SNP, SumStat)

(2) 

- (선별된 SNP, SumStat) >> clumping (rsq < 0.1~0.2; 250kb) >> PRS 계산 완료


## Genetic Risk Score (GRS) 

- 위 과정에서 clumping 기법 대신 기존 논문에서 보고된 SNP들로 한번 더 필터링 시킴
- 만약, 기존 논문에서 gene이 보고되었다면, NCBI에서 해당 gene의 위치정보를 가지고 와서 +-250kb까지 허용하여 해당 SNP만 가지고 PRS 계산을 하되, high correlation 문제가 생길 수 있기 때문에 clumping을 한번 더 실시함. 즉, (선별된 X + SumStat) >> 논문에서 보고된 SNP 필터링 >> (if high-correlation) clumping >> PRS 추출




In public database, collect SNPs associated with our phenotype of interest.

- public databases
  * GWAS catalog (Multi-ethnic, 6475): https://www.ebi.ac.uk/gwas/downloads/summary-statistics
  * Global Biobank Engine (Multi-ethnic, >2000): https://biobankengine.stanford.edu
  * Biobank Japan (East Asian, 224): https://pheweb.jp/downloads
  * T2D AMP portal (Multi-ethnic, 292): https://t2d.hugeamp.org


## If summary statistics is available,

관심있는 질환과 연관되어있는 **SNP**을 알고 있다고 가정하자.

```plink --bfile FILE --score SUMMARY.txt 2 4 7 header sum include-cnt double-dosage --out PRS_RESULT```




### Genetic Risk Score
기존 논문에서 검증된 SNP에 대해서만 PRS를 계산하겠다. 

> 연관있는 유전자는 찾았지만 SNP은 없는 경우
NCBI에서 유전자의 위치 정보를 가져와서 +- 250kb를 고려하여 해당되는 SNP 정보를 reference panel에서 가져옴.


(아래 정리해야함 - start)
(1) Find a published paper related to our phenotype of interest
(2) Collect SNPs associated with the phenotype over the published papers
(3) Access the GWAS catalog (https://www.ebi.ac.uk/gwas/) and search our phenotype
(4) Get SNPs with 'association count' as higher as possible.



그러면 해당 SNP **Reference Genome panel**

Get the genome datasets with the corresponding samples to the race (Asian or European) on [1000Genome](https://www.internationalgenome.org/data-portal/sample) phase 1
- East Asian: CHB (Han Chinese in Beijing) + JPT (Japanese) on 1000 Genomes phase 1
- European: CEPH on 1000 Genome phase 1

> GWAS catalog 등에 원하는 질환 관련 GENE이 없는 경우
http://amp.pharm.mssm.edu/Harmonizome 등에서 폭넓게 한번 더 찾아본다.

찾아진 경우, NCBI에서 해당 GENE의 위치를 찾는다. 찾아진 위치에 +-250kb까지 확인을 한다.
즉, FromBP = GENE 시작 position - 250kb, ToBP = GENE 끝 position + 250kb 

```plink --bfile file --chr CHR --from-bp FromBP --to-bp ToBP --make-bed --out newfile```

각 유전자에 대해서 위 과정을 반복한다.

그리고 reference genome에서 각 SNP의 association 결과를 저장함.

(아래 정리해야함 - end)



### Clumping
### LDpred, PRSice, PRScs

## If GWAS results do not exist or summary statistics are not available,
### LOGO (leave on group out)


