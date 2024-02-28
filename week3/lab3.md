# Lab Week 3. Sociogenomics

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/nicolabarban/sociogenomics2023&cloudshell_tutorial=week3/lab3.md)

## Description
In this lab we will learn:

* Compute descripitve statistics with PLINK on genetic files 
* Quality control procedure with plink
* Run association analysis with PLINK

## Part I . Managing files and directories.
Let's have a look a the file. 

```
cd $HOME/Sociogenomics
rm Data/*.*

wget -O Data/lab_week3.zip https://www.dropbox.com/s/wum0okiq4dbn9p0/week4.zip?dl=0 
unzip -o -d Data/ Data/lab_week4.zip 
mv Data/week4/*.*  Data/ 
rm -r Data/week4/
```



### Select individuals
```
./plink --bfile Data/hapmap-ceu \
        --keep Data/list.txt \
        --make-bed --out  Results/selectedIndividuals

```

### Select individuals with genotype at least 95% complete
We can select individuals based on the completness of their genotype
```

./plink --bfile Data/hapmap-ceu --make-bed --mind 0.05 --out Results/highgeno
```



### Select specific markers

In this way we select only a specific marker, in this case SNP `rs9930506`
```

./plink     --bfile Data/hapmap-ceu \
            --snps  rs9930506 \
        	--make-bed \
            --out  Results/rs9930506sample

```



### Add a phenotype into PLINK files

PLINK file can also store info on a phenotype
```


head Data/1kg_EU_qc.fam
```

```

head Data/1kg_EU_qc.bim
```


This file contains info on BMI of different individuals
```

head Data/BMI_pheno.txt
```

This is how we add a phenotipic information to a plink file
```


./plink      --bfile Data/1kg_EU_qc \
             --pheno Data/BMI_pheno.txt \
             --make-bed --out Results/1kg_EU_BMI

```

```
 head Data/1kg_EU_BMI.fam
```
## Part II. Descriptive Statistics

### Allele frequency
We can calculate allele frequency

```
 ./plink --bfile Data/hapmap-ceu  --freq --out Results/Allele_Frequency
```

```
head Results/Allele_Frequency.frq 
```

### Missing values

individuals
```


./plink --bfile Data/hapmap-ceu --missing --out Results/missing_data
```
variants
```

head Data/missing_data.imiss
```

Filter females
```

./plink     --bfile Data/hapmap-ceu \
            --filter-females \
            --make-bed \
            --out Results/hapmap_filter_females

```


```
 head Data/1kg_EU_BMI.fam
```

how many observations?

```
 wc -l Data/1kg_EU_BMI.fam
```

how many variants?

```
 wc -l Data/1kg_EU_BMI.bim
```


## Allele Frequency



Allele frequency
```
 ./plink --bfile Data/hapmap-ceu  --freq --out Results/Allele_Frequency
```

```
head Results/Allele_Frequency.frq 
```

### Missing values

individuals
```
./plink --bfile Data/hapmap-ceu --missing --out Results/missing_data
```
variants
```

head Data/missing_data.imiss
```

Filter females
```

./plink     --bfile Data/hapmap-ceu \
            --filter-females \
            --make-bed \
       	 	--out Results/hapmap_filter_females

```
## Quality control
```


./plink --bfile Data/1kg_EU_BMI \
		--mind 0.05 \
		--make-bed \
		--out Results/1kg_hm3_mind005
```

Calculate heterozygocity
```

./plink --bfile Data/1kg_EU_BMI \
		--het --out Results/1kg_hm3_het
```


Low call-rate SNPS
```

./plink --bfile Data/1kg_EU_BMI \
		--geno 0.05 \
		--make-bed \
		--out Results/1kg_hm3_geno
```

Allele frequency
```

./plink --bfile Data/1kg_EU_BMI \
	 	--maf 0.01 \
		--make-bed  --out Results/1kg_hm3_maf
```
deviation from HWE
```

./plink --bfile Data/1kg_EU_BMI \
	 	--hwe 0.00001 \
		--make-bed  --out Results/1kg_hm3_hwe

```

## Combine different commands in one go

**PLINK QC**. combine different commands in one go
```


./plink     --bfile Data/1kg_EU_BMI \
       	--mind 0.03 \
       	--geno 0.05 \
       	--maf 0.01 \
    	--hwe 0.00001 \
        --make-bed  --out Results/1kg_hm3_QC      
			
```
## Linkage disequilibrium

Calculate linkage disequilibrium
```
./plink --bfile Data/hapmap-ceu \
	 	--ld rs2883059 rs2777888 \
		--out Results/ld_example

```