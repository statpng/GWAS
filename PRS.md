
# Polygenic Risk Score (PRS)

## Clumping을 이용한 PRS 계산

> X: SNPs in our data <br>
> SumStat: Summary Statistics <br>
> RefPanel: Reference Panel

1. SumStat 정보가 있는 경우
   - SumStat을 계산하는데 사용된 RefPanel을 준비함
   - X 중에서 RefPanel에 있는 SNP만 추출함
   - 선별된 X + SumStat + clumping (rsq < 0.1~0.2; 250kb)을 이용해 PRS 계산
2. SumStat 없는 경우
   - RefPanel을 하나 준비함 (why?)
   - X 중에서 RefPanel에 있는 SNP만 추출함
   - 선별된 X에 대해 보유한 데이터로 summary statistics를 계산함 (1번과의 차이점)
   - 선별된 X + 우리 데이터로 계산한 SumStat + clumping을 이용해 PRS 계산


## Genetic Risk Score (GRS) 

- 위 과정에서 clumping 기법 대신 기존 논문에서 보고된 SNP들로 필터링 시키면 됨.
- 만약, 기존 논문에서 gene이 보고되었다면, NCBI에서 해당 gene의 위치정보를 가지고 와서 +-250kb까지 허용하여 해당 SNP만 가지고 PRS 계산을 하되, high correlation 문제가 생길 수 있기 때문에 clumping을 한번 더 실시함.
- 즉, (선별된 X + SumStat) >> 논문에서 보고된 SNP 필터링 >> (if high-correlation※) clumping >> PRS 추출

※ Gene에 의해 SNP을 선별한 경우를 포함함.


## 요약

즉, (1) RefPanel 준비, (2) RefPanel 기반 SNP 필터링, (3) SumStat 준비 또는 계산, (4) 기보고된 논문 or Clumping (rsq < 0.1~0.2; 250kb) 이용 SNP 추출, (5) 만약 기존 논문에서 gene이 보고되었다면 clumping 추가 실시, (6) PRS 계산 완료





## Public database

In public database, collect SNPs associated with our phenotype of interest.

- public SumStat databases
  * GWAS catalog (Multi-ethnic, 6475): https://www.ebi.ac.uk/gwas/downloads/summary-statistics
  * Global Biobank Engine (Multi-ethnic, >2000): https://biobankengine.stanford.edu
  * Biobank Japan (East Asian, 224): https://pheweb.jp/downloads
  * T2D AMP portal (Multi-ethnic, 292): https://t2d.hugeamp.org


- Disease-associated GENE database
   * http://amp.pharm.mssm.edu/Harmonizome





## Reference
- 유전적 위험도 측정 가이드라인(v1.0) by KNIH


