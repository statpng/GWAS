``` ukbconv <ukb23456.enc_ukb> r -s20002 ``` <br>
``` ukbconv <ukb23456.enc_ukb> r -i <include.txt> ``` <br>
``` ukbconv <ukb23456.enc_ukb> r -x <exclude.txt> ``` <br>

``` plink2 --bgen <.bgen file> ref-last --sample <.sample file> --keep <.txt file containing SampleID per row> --make-bed --out <output name> ``` <br>
``` plink2 --bgen <.bgen file> ref-last --sample <.sample file> --keep <.txt file containing SampleID per row> --make-bgen --out <output name> ``` <br>
``` plink2 --bgen <.bgen file> ref-last --sample <.sample file> --keep <.txt file containing SampleID per row> --export bgen-1.2 --out <output name> ``` <br>
