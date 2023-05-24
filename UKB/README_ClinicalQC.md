This package combines some features from both ukbwranglr and ukbtools, and we express our gratitude to their contributions.



## Basic Usages



```
# List of consent withdrawers (N=43) at 23.04.25.
length( png.sample.exclude() )
# [1] 43
```


### Library
```
# remotes::install_github("statpng/png.ukb")
# detach("package:png.ukb", unload=TRUE)
library(png.ukb)

library(tidyverse)
library(devtools)
library(dplyr)
library(tidyr)
library(tidyselect)
library(readr)
library(tibble)

source("https://raw.githubusercontent.com/statpng/pngfunction/master/tidyverse/functions.R")
```



자주 사용되는 오브젝트들입니다. **여기서 path, filename, path_split은 각자의 환경에 맞춰서 변경하셔야합니다.**
path와 filename은 main dataset에 대한 경로와 파일명이고, path_split은 splitted dataset에 대한 경로입니다.

```
{
  path <- "/Volumes/png1/Yonsei/3. UKB/Clinical/Maindata3/"
  filename <- "ukb669778"
  path_split <- "/Volumes/png1/2.UKB/UKB_ClinicalData_descriptive"
  
  ukb_main <- paste0(path, filename, ".tab", collapse="")
  ukb_field <- get_ukb_field(filename, path)
  ukb_dict <- get_ukb_dict()
  ukb_coding <- get_ukb_codings()
  ukb_data_dict <- make_data_dict( ukb_main=ukb_main, ukb_dict=ukb_dict)
  ukb_data_dict_path <- png.dict2path(ukb_data_dict) # split Path to path1:path5
  # New path categories with at most 2000 variables
  ukb_path <- ukb_data_dict_path %>% select(path1:path5) %>% png.path.NestOverN(N=2000)
}
```
- `ukb_main`: 메인 데이터에 대한 경로를 포함한 파일명.
- `ukb_field`: 메인 데이터 추출시 생성되는 각 변수에 대한 설명.
- `ukb_dict`: UKB에서 제공되는 dictionary 파일.
- `ukb_coding`: 각 변수에서 사용되는 코딩북 (e.g., 1=남자, 2=여자 등).
- `ukb_data_dict`: `ukb_dict` 중에서 현재 입력된 `ukb_main`에 맞춰서 추출된 버전.
- `ukb_data_dict_path`: `ukb_data_dict`에서 5단계 변수 카테고리 (Path)를 각각 변수로 나눈 버전 (중요하지 않음).
- `ukb_path`: 데이터를 분리 저장하기 위해 기존 "5단계 변수 카테고리"에서 정의된 새로운 카테고리.


자세한 내용은 다음과 같습니다.
```
{
  ukb_field %>% str
  # 'data.frame':	20653 obs. of  5 variables:
  # $ field.showcase: chr  "eid" "3" "3" "3" ...
  # $ field.html    : chr  "eid" "3-0.0" "3-1.0" "3-2.0" ...
  # $ field.tab     : chr  "f.eid" "f.3.0.0" "f.3.1.0" "f.3.2.0" ...
  # $ col.type      : chr  "Sequence" "Integer" "Integer" "Integer" ...
  # $ col.name      : chr  "eid" "verbal_interview_duration_f3_0_0" "verbal_interview_duration_f3_1_0" "verbal_interview_duration_f3_2_0" ...
  
  ukb_dict %>% str
  # Classes ‘data.table’ and 'data.frame':	9079 obs. of  17 variables:
  # $ Path        : chr  "Assessment centre > Procedural metrics > Process durations" "Assessment centre > Procedural metrics > Process durations" "Assessment centre > Procedural metrics > Process durations" "Assessment centre > Procedural metrics > Process durations" ...
  # $ Category    : chr  "152" "152" "152" "152" ...
  # $ FieldID     : chr  "3" "4" "5" "6" ...
  # $ Field       : chr  "Verbal interview duration" "Biometrics duration" "Sample collection duration" "Conclusion duration" ...
  # $ Participants: chr  "501528" "498017" "501201" "499062" ...
  # $ Items       : chr  "585037" "586338" "591369" "589111" ...
  # $ Stability   : chr  "Complete" "Complete" "Complete" "Complete" ...
  # $ ValueType   : chr  "Integer" "Integer" "Integer" "Integer" ...
  # $ Units       : chr  "seconds" "seconds" "seconds" "seconds" ...
  
  ukb_coding %>% str
  # Classes ‘data.table’ and 'data.frame':	532346 obs. of  3 variables:
  # $ Coding : chr  "1" "2" "2" "2" ...
  # $ Value  : chr  "1" "0" "1" "11" ...
  # $ Meaning: chr  "Yes" "Other job (free text entry)" "Managers and Senior Officials" "Corporate Managers" ...
  # - attr(*, ".internal.selfref")=<externalptr> 
  
  ukb_data_dict %>% str
  # tibble [20,653 × 22] (S3: tbl_df/tbl/data.frame)
  # $ descriptive_colnames: chr [1:20653] "eid" "verbal_interview_duration_f3_0_0" "verbal_interview_duration_f3_1_0" "verbal_interview_duration_f3_2_0" ...
  # $ colheaders_raw      : chr [1:20653] "f.eid" "f.3.0.0" "f.3.1.0" "f.3.2.0" ...
  # $ colheaders_processed: chr [1:20653] "feid" "f3_0_0" "f3_1_0" "f3_2_0" ...
  # $ FieldID             : chr [1:20653] "eid" "3" "3" "3" ...
  # $ instance            : chr [1:20653] NA "0" "1" "2" ...
  # $ array               : chr [1:20653] NA "0" "0" "0" ...
  # $ Path                : chr [1:20653] NA "Assessment centre > Procedural metrics > Process durations" "Assessment centre > Procedural metrics > Process durations" "Assessment centre > Procedural metrics > Process durations" ...
  # $ Category            : chr [1:20653] NA "152" "152" "152" ...
  # $ Field               : chr [1:20653] "Participant identifier ('eid')" "Verbal interview duration" "Verbal interview duration" "Verbal interview duration" ...
  
  ukb_dict$Path %>% head
  # [1] "Assessment centre > Procedural metrics > Process durations"                
  # [2] "Assessment centre > Procedural metrics > Process durations"                
  # [3] "Assessment centre > Procedural metrics > Process durations"                
  # [4] "Assessment centre > Procedural metrics > Process durations"                
  # [5] "Assessment centre > Physical measures > Bone-densitometry of heel"         
  # [6] "Assessment centre > Physical measures > Anthropometry > Body size measures"
  
  ukb_data_dict_path %>% select(path1:path5) %>% head
  # path1             path2              path3             path4 path5
  # <chr>             <chr>              <chr>             <chr> <chr>
  #   1 NA                NA                 NA                NA    NA   
  # 2 Assessment centre Procedural metrics Process durations NA    NA   
  # 3 Assessment centre Procedural metrics Process durations NA    NA   
  # 4 Assessment centre Procedural metrics Process durations NA    NA   
  # 5 Assessment centre Procedural metrics Process durations NA    NA   
  # 6 Assessment centre Procedural metrics Process durations NA    NA 
  
  
  ukb_path %>% table %>% sort %>% tail %>% as.matrix
  # Online follow-up > Work environment                         1309
  # Assessment centre > Physical measures > ECG during exercise 1411
  # Assessment centre > Verbal interview                        1680
  # Assessment centre > Imaging > Brain MRI                     1827
  # Health-related outcomes > Hospital inpatient                1849
  # Online follow-up > Diet by 24-hour recall                   1946
}
```


새로 정의된 `ukb_path`에 맞춰서 데이터를 분리 저장합니다. 이때, 최대 2000개의 변수를 포함하도록 데이터를 저장합니다.

```
# ukb_path:  This takes a long time
if(FALSE){
  tab <- table(ukb_path) %>% sort(decreasing=TRUE)
  title <- names(tab)
  
  library(data.table)
  for( i in 1:length(tab) ){
    
    wh <- c(1, which( ukb_path == names(tab)[i] ))
    
    ukb_data_dict_filtered <- 
      ukb_data_dict %>% 
      slice(wh)
    
    ukb_data_filtered <- 
      read_ukb( path = ukb_main,
                descriptive_colnames = TRUE,
                label = FALSE, 
                ukb_data_dict = ukb_data_dict_filtered )
    
    fwrite(ukb_data_filtered, paste0("ukb_data - ", title[i], ".csv") )
    
    gc()
    rm(ukb_data_filtered)
  }
  
}
```





UKB에서 사용되는 변수명에는 크게 descriptive ID와 field ID가 있는데, 본 패키지에서는 descriptive ID를 기본으로 사용합니다.
따라서, field ID를 사용하고자 하는 경우 아래 `png.desc2fid`를 이용하여 변환하여 사용하시길 바랍니다.

```
# Types of col.names
var_demographics <- png.demographic_vars(type="descriptive")

## (1) Descriptive (default)
var_demographics %>% head
# [1] "eid"                           "sex_f31_0_0"                   "genetic_sex_f22001_0_0"       
# [4] "year_of_birth_f34_0_0"         "month_of_birth_f52_0_0"        "age_at_recruitment_f21022_0_0"

## (2) Field ID (fid)
png.desc2fid(var_demographics, simple=FALSE) %>% head
# [1] "f.eid"       "f.31.0.0"    "f.22001.0.0" "f.34.0.0"    "f.52.0.0"    "f.21022.0.0"

## (3) Simplified Field ID
png.desc2fid(var_demographics, simple=TRUE) %>% head
# [1] "f.eid"   "f.31"    "f.22001" "f.34"    "f.52"    "f.21022"
```






### Load data from splitted data files
본 패키지에는 기본적으로 (1) demographics, (2) diagnoses, (3) icd-related 등의 변수집합을 제공하고 있습니다.
`png.var.demographics`, `png.var.disease`, `png.var.genetics`를 이용하면 불러올 수 있습니다.
아래 예시를 통해 각 변수집합에 있는 변수들을 불러오는 방법에 대해서 소개하겠습니다.


#### (1) demographics

공통적으로 splitted datasets로부터 데이터를 불러오기때문에 `path_split`가 요구됩니다. 
그리고 입력된 변수집합 (`vars`)이 어떤 카테고리에 포함되는지 확인하기 위해 `ukb_data_dict`와 `ukb_path`가 필요합니다.
`exact`는 해당 변수명과 정확히 일치하는 변수들을 불러올 것인지를 확인합니다. 
예를 들어, `month_of_birth_f52_0_0`를 불러오고자 하는 경우, `exact=FALSE`로 설정하면 `month_of_birth_f52`에 해당하는 변수들도 불러옵니다.

```
var_demographics <- png.var.demographics(type="descriptive")

ukb_data_demographics <- 
  png.ukb_read(path=path_split, 
               vars=var_demographics, 
               ukb_data_dict=ukb_data_dict, 
               ukb_path=ukb_path,
               exact=FALSE, exclude=png.sample.exclude())
# exact >> TRUE: f40007_0_0; FALSE: f40007
# [1] 502401    498


ukb_data_dict_demographics <- ukb_data_dict %>% 
  filter( FieldID %in% gsub( "f.", "", png.desc2fid(var_demographics, simple=TRUE) ) )

data.table::fwrite(ukb_data_demographics, paste0("ukb_data_demographics.csv") )
data.table::fwrite(ukb_data_dict_demographics, paste0("ukb_data_dict_demographics.csv") )
```


#### (2) diagnoses
```
var_diagnoses <- png.var.disease(ukb_data_dict, regexpr=c("diagno", "icd", "death")[1])

png.var.disease(ukb_data_dict, regexpr=c("diagno", "icd", "death")[1]) %>% 
  stringr::str_extract("(.*)\\_\\d+\\_\\d+", group=1) %>% table
png.var.disease(ukb_data_dict, regexpr=c("diagno", "icd", "death")[2]) %>% 
  stringr::str_extract("(.*)\\_\\d+\\_\\d+", group=1) %>% table
png.var.disease(ukb_data_dict, regexpr=c("diagno", "icd", "death")[3]) %>% 
  stringr::str_extract("(.*)\\_\\d+\\_\\d+", group=1) %>% table


ukb_data_diagnoses <- 
  png.ukb_read(path=path_split, 
               vars=var_diagnoses, 
               ukb_data_dict=ukb_data_dict, 
               ukb_path=ukb_path,
               exact=FALSE, exclude=png.sample.exclude())

ukb_data_dict_diagnoses <- 
  ukb_data_dict %>% 
  filter( FieldID %in% gsub( "f.", "", png.desc2fid(var_diagnoses, simple=TRUE) ) )

data.table::fwrite(ukb_data_diagnoses, paste0("ukb_data_diagnoses.csv") )
data.table::fwrite(ukb_data_dict_diagnoses, paste0("ukb_data_dict_diagnoses.csv") )
```



##### icd-related

`ukb_icd_diagnosis`: 입력된 id에 대하여 ICD (9/10) 기준 진단된 질환이 있는지를 확인
`ukb_icd_code_meaning`: 입력된 ICD code가 주어진 ICD 버전에서 어떤 의미를 갖는지 확인
`ukb_icd_keyword`: 입력된 (정규표현식) 문자열에 대한 ICD code를 제공. 예를 들어, cardio를 입력하면 meaning에 cardio가 포함된 모든 ICD code를 반환함.
`ukb_icd_prevalence`: 현재 데이터에서 주어진 ICD code의 prevalance를 계산해줌. 정규표현식을 지원함.

```
ukb_icd_diagnosis(ukb_data_diagnoses, id=1000000+c(15, 27), icd.version=10)
# 9  1000015 Z961                                               Z96.1 Presence of intraocular lens
# 10 1000015 Z966                                     Z96.6 Presence of orthopaedic joint implants
# 11 1000015 H269                                                      H26.9 Cataract, unspecified
# 12 1000015 H251                                                    H25.1 Senile nuclear cataract
# 13 1000027 D370                                               D37.0 Lip, oral cavity and pharynx
# 14 1000027  K30                                                                    K30 Dyspepsia
# 15 1000027 K801                           K80.1 Calculus of gallbladder with other cholecystitis
ukb_icd_code_meaning(icd.code = "E10", icd.version = 10)
#    code   meaning
# 1  E10    E10 Insulin-dependent diabetes mellitus

ukb_icd_keyword("coronary", icd.version = 10) %>% head

ukb_icd_keyword("cardio|atrial", icd.version = 10) %>% head
#   code                                                   meaning
# 1 B334 B33.4 Hantavirus (cardio-)pulmonary syndrome [HPS] [HCPS]
# 2 A520                             A52.0 Cardiovascular syphilis
# 3 A439                            A43.9 Nocardiosis, unspecified
# 4 A438                          A43.8 Other forms of nocardiosis
# 5 A431                               A43.1 Cutaneous nocardiosis
# 6 A430                               A43.0 Pulmonary nocardiosis

ukb_icd_prevalence(ukb_data_diagnoses, icd.code="H401", icd.version = 10)
# [1] 0.007143696

# ukb_icd_prevalence(ukb_data_diagnoses, icd.code="E1[0-1]", icd.version = 10)
```






#### (3) Genetics
Genetic data의 Sample QC에서 사용되는 변수들이 포함된 데이터.
Sample QC 이후, N=406,644이 남는 것으로 확인됨. 여기서 동의철회자 43명 (중복 제외 27명)이 제외되면, 최종적으로 GWAS에서 사용될 샘플은 N=406,627이 됨.

```  
var_genetics <- png.var.genetics()

ukb_data_dict_genetics <- ukb_data_dict %>% 
  filter( FieldID %in% gsub( "f.", "", png.desc2fid(var_genetics, simple=TRUE) ) )

ukb_data_genetics <- 
  png.ukb_read(path=path_split, 
               vars=var_genetics, 
               ukb_data_dict=ukb_data_dict, 
               ukb_path=ukb_path,
               exact=FALSE, exclude=png.sample.exclude())

ukb_data_genetics_AfterQC <- ukb_data_genetics %>% 
  filter(is.na(sex_chromosome_aneuploidy_f22019_0_0), # != 1,
         sex_f31_0_0 == genetic_sex_f22001_0_0, # sex_discordance
         is.na(outliers_for_heterozygosity_or_missing_rate_f22027_0_0), # != 1,
         # missing_rate_in_autosome < 0.02,
         used_in_genetic_principal_components_f22020_0_0 == 1 # relatedness >= 3
  )

ukb_data_genetics_AfterQC_coding <- png.df2coding(ukb_data_genetics_AfterQC, ukb_data_dict, ukb_coding)


ukb_data_genetics_AfterQC_coding %>% dim
# [1] 406644     57

ukb_data_genetics %>% filter(!is.na(sex_chromosome_aneuploidy_f22019_0_0)) %>% nrow
# [1] 651
ukb_data_genetics %>% filter(!sex_f31_0_0 == genetic_sex_f22001_0_0) %>% nrow
# [1] 372
ukb_data_genetics %>% filter(!is.na(outliers_for_heterozygosity_or_missing_rate_f22027_0_0)) %>% nrow
# [1] 968
ukb_data_genetics %>% filter(is.na(used_in_genetic_principal_components_f22020_0_0)) %>% nrow
# [1] 95363
nrow(ukb_data_genetics) - nrow(ukb_data_genetics_AfterQC_coding)
# [1] 95757

write.table(ukb_data_genetics_AfterQC$eid, file="ukb_data_genetics_AfterQC_eid.txt", row.names = FALSE, col.names = FALSE)


data.table::fwrite(ukb_data_genetics, "ukb_data_genetics.csv" )
data.table::fwrite(ukb_data_dict_genetics, "ukb_data_dict_genetics.csv" )
data.table::fwrite(ukb_data_genetics_AfterQC, "ukb_data_genetics_AfterQC.csv" )
data.table::fwrite(ukb_data_genetics_AfterQC_coding, "ukb_data_genetics_AfterQC_coding.csv" )

```







##### png.df2coding
현재 불러온 데이터는 numeric value로 코딩된 암호화된 형태이기 때문에, `png.df2coding`을 통해 각 입력된 코드 값들이 실제로 의미하는 범주로 변환합니다.

```
ukb_data_demographics %>% 
  select(sex_f31_0_0, genetic_sex_f22001_0_0) %>% 
  table()

ukb_data_demographics %>% 
  select(sex_f31_0_0, genetic_sex_f22001_0_0) %>% 
  png.df2coding(ukb_data_dict, ukb_coding) %>% 
  table()
```

#### png.filter_visit
N차 방문중에서 특정 차수의 변수만을 추출해주는 함수. 추가적으로 특정 인스턴스만을 추출하는 기능도 제공함.

```
ukb_data_demographics_visit0 <- png.filter_visit(ukb_data_demographics, visit=0, instance="\\d+")
ukb_data_demographics_visit0_instance0 <- png.filter_visit(ukb_data_demographics, visit=0, instance=0)
```

##### elucidate::describe_all
불러온 데이터를 살펴보기 위해서는 R에서 기본으로 제공하는 또는 다른 일반적인 패키지에서 제공하는 함수를 이용할 경우 많은 시간이 소요됩니다.
따라서, `data.table` 패키지를 기반으로하는 `elucidate` 패키지를 소개합니다.
이로써 각 변수들의 요약 통계량을 빠른 시간 내에 살펴볼 수 있습니다.

```
library(elucidate)
ukb_data_demographics %>% elucidate::describe_na_all()
ukb_data_demographics %>% elucidate::describe_all(class = c("d","f","c","l","n")[1] )
ukb_data_demographics %>% elucidate::describe_all(class = "all" )
```






### Timing comparison before and after splitting

```
pathname <- png.which_path("genotype_measurement_plate_f22007_0_0", ukb_data_dict, ukb_path, exact=TRUE)
fullname <- paste0(path_split, "/ukb_data - ", pathname, ".csv")

system.time(data.table::fread(fullname, nrows=1e4))
# user  system elapsed 
# 0.092   0.011   0.150 
system.time(data.table::fread(fullname, nrows=1e4, select=c("eid", "genotype_measurement_plate_f22007_0_0")))
# user  system elapsed 
# 0.040   0.002   0.042 
system.time(data.table::fread(ukb_main, nrows=1e4))
# user  system elapsed 
# 14.054   1.156  18.423 
system.time(data.table::fread(ukb_main, nrows=1e4, select=c("f.eid", "f.22007.0.0")))
# user  system elapsed 
# 5.711   0.447   6.177 
```




### Missingness & Correlation

UKB 데이터에는 weight, height, BMI 등을 측정하는데 있어서 여러 버전의 변수명이 존재함.
이들 중에서 어떤 변수명을 사용할지 결정하기 위해 이들의 결측치 분포와 correlation을 살펴보고자 함.

#### Missingness
```
library(UpSetR)
library(naniar)

{
  ukb_data_weight <- png.ukb_read_regexpr("weight_", 
                                          ukb_data_dict=ukb_data_dict, 
                                          path_split=path_split, 
                                          ukb_path=ukb_path, exclude=png.sample.exclude())
  ukb_data_weight %>% png.filter_visit(0) %>% naniar::gg_miss_upset(nsets=40)
  # >> weight_f21002_0_0,  weight_f23098_0_0
}


{
  ukb_data_height <- png.ukb_read_regexpr("height_", 
                                          ukb_data_dict=ukb_data_dict, 
                                          path_split=path_split, 
                                          ukb_path=ukb_path, exclude=png.sample.exclude())
  
  ukb_data_height %>% naniar::gg_miss_upset(nsets=30)
  # >> standing_height_f50_0_0, height_f12144_2_0
}

{
  ukb_data_bmi <- png.ukb_read_regexpr("_bmi_", 
                                       ukb_data_dict=ukb_data_dict, 
                                       path_split=path_split, 
                                       ukb_path=ukb_path, exclude=png.sample.exclude())
  
  ukb_data_bmi %>% naniar::gg_miss_upset(nsets=30)
}
```


각 카테고리별로 missingness가 적은 상위 10개의 변수명을 `elucidate::describe_na_all()`을 이용하여 살펴볼 수 있음.

```
{
  ukb_data_alcohol <- png.ukb_read_regexpr("alcohol_", 
                                       ukb_data_dict=ukb_data_dict, 
                                       path_split=path_split, 
                                       ukb_path=ukb_path, exclude=png.sample.exclude())
  ukb_data_alcohol %>% png.filter_visit(0) %>% naniar::gg_miss_upset(nsets=50)
  # alcohol_intake_frequency_f1558_0_0, alcohol_drinker_status_f20117_0_0
  
  ukb_data_alcohol %>% png.filter_visit(0) %>% elucidate::describe_na_all() %>% arrange(p_na) %>% head(10)
  #                                                                                          variable  cases      n     na   p_na
  # 1:                                                                                            eid 502401 502401      0 0.0000
  # 2:                                                             alcohol_intake_frequency_f1558_0_0 502401 501502    899 0.0018
  # 3:                                                              alcohol_drinker_status_f20117_0_0 502401 501502    899 0.0018
  # 4:                                            alcohol_intake_versus_10_years_previously_f1628_0_0 502401 460272  42129 0.0839
  # 5:                                                     alcohol_usually_taken_with_meals_f1618_0_0 502401 387256 115145 0.2292
  # 6:                                          reason_for_reducing_amount_of_alcohol_drunk_f2664_0_0 502401 207041 295360 0.5879
  # 7: ever_had_known_person_concerned_about_or_recommend_reduction_of_alcohol_consumption_f20405_0_0 502401 157310 345091 0.6869
  # 8:                  ever_been_injured_or_injured_someone_else_through_drinking_alcohol_f20411_0_0 502401 157310 345091 0.6869
  # 9:                                                       frequency_of_drinking_alcohol_f20414_0_0 502401 157310 345091 0.6869
  # 10:                                  amount_of_alcohol_drunk_on_a_typical_drinking_day_f20403_0_0 502401 143632 358769 0.7141
}
```


```
{
  ukb_data_smoke <- png.ukb_read_regexpr("smoking|smoke", 
                                       ukb_data_dict=ukb_data_dict, 
                                       path_split=path_split, 
                                       ukb_path=ukb_path, exclude=png.sample.exclude())
  ukb_data_smoke %>% png.filter_visit(0, 0) %>% naniar::gg_miss_upset(nsets=50)
  
  ukb_data_smoke %>% elucidate::describe_na_all() %>% arrange(p_na) %>% head(10)
  #                                            variable  cases      n     na   p_na
  # 1:                                              eid 502401 502401      0 0.0000
  # 2:                current_tobacco_smoking_f1239_0_0 502401 501508    893 0.0018
  # 3:                        smoking_status_f20116_0_0 502401 501508    893 0.0018
  # 4:                           ever_smoked_f20160_0_0 502401 499515   2886 0.0057
  # 5:          maternal_smoking_around_birth_f1787_0_0 502401 494144   8257 0.0164
  # 6:      exposure_to_tobacco_smoke_at_home_f1269_0_0 502401 462677  39724 0.0791
  # 7: exposure_to_tobacco_smoke_outside_home_f1279_0_0 502401 462677  39724 0.0791
  # 8:                   past_tobacco_smoking_f1249_0_0 502401 462277  40124 0.0799
  # 9:           smoking_smokers_in_household_f1259_0_0 502401 458907  43494 0.0866
  # 10:                pack_years_of_smoking_f20161_0_0 502401 150916 351485 0.6996
}

```



#### Correlation

결측치가 가장 적은 두개의 변수에 대해 상관계수를 살펴보면 유사성이 굉장히 높은 것으로 나타남.
```
ukb_data_weight %>% select(weight_f21002_0_0, weight_f23098_0_0) %>% cor(., use = "complete.obs") %>% .[2]
# [1] 1
ukb_data_height %>% select(standing_height_f50_0_0, height_f12144_2_0) %>% cor(., use = "complete.obs") %>% .[2]
# [1] 0.9730143
ukb_data_height %>% select(standing_height_f50_0_0, standing_height_f50_2_0) %>% cor(., use = "complete.obs") %>% .[2]
# [1] 0.9903608
ukb_data_bmi %>% select(body_mass_index_bmi_f21001_0_0, body_mass_index_bmi_f23104_0_0) %>% cor(., use = "complete.obs") %>% .[2]
# [1] 0.99992
```













## For PLINK
```
var_demographics <- png.demographic_vars()

ukb_data_demographics <- 
  png.ukb_read(path=path_split, 
               vars=var_demographics, 
               ukb_data_dict=ukb_data_dict, 
               ukb_path=ukb_path,
               exact=FALSE, exclude=png.sample.exclude())

ukb_data_demographics[,c("Age","Sex","AgeSex","Age2","Age2Sex"):=list(age_at_recruitment_f21022_0_0,
                                                                      sex_f31_0_0,
                                                                      age_at_recruitment_f21022_0_0*sex_f31_0_0,
                                                                      age_at_recruitment_f21022_0_0^2,
                                                                      age_at_recruitment_f21022_0_0^2*sex_f31_0_0)]


png.write_pheno( ukb_data_demographics, 
                 file = "ukb_data_demographics.pheno", 
                 vars = c("Age","Sex","AgeSex","Age2","Age2Sex",
                          "sex_f31_0_0",
                          "year_of_birth_f34_0_0",
                          "age_at_recruitment_f21022_0_0", 
                          "standing_height_f50_0_0",
                          "weight_f21002_0_0", 
                          "body_mass_index_bmi_f21001_0_0",
                          "body_fat_percentage_f23099_0_0",
                          "genetic_principal_components_f22009_0_(20|[1-9]|1[0-9])$"),
                 type = "plink")



# For each GWAS conducted for each phenotype and ancestry group, we included the following covariates:
#   
# Age
# Sex
# Age * Sex
# Age2
# Age2 * Sex
# The first 20 PCs
```
