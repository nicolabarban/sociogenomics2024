# Lab Week 5. Sociogenomics

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/nicolabarban/sociogenomics2023&cloudshell_tutorial=week5/lab5.md)

## Description
In this lab we will learn:
* Calculate IBS and relatedness
* Association analys

## Part I . Managing files and directories. (no need after week 4, just repeating the command)
Let's have a look a the file. 

```
cd $HOME/Sociogenomics
rm Data/*.*

wget -O Data/lab_week4.zip https://www.dropbox.com/s/wum0okiq4dbn9p0/week4.zip?dl=0 
unzip -o -d Data/ Data/lab_week4.zip 
mv Data/week4/*.*  Data/ 
rm -r Data/week4/
```


**PLINK QC**. combine different commands in one go
```


./plink     --bfile Data/1kg_EU_BMI \
       	--mind 0.03 \
       	--geno 0.05 \
       	--maf 0.01 \
    	--hwe 0.00001 \
        --make-bed  --out Results/1kg_hm3_QC      
			
```
## Select independent SNPS

Calculate linkage disequilibrium
```
./plink --bfile Data/1kg_hm3_qc \
	 	--ld rs1048488 rs3115850 \
		--out Results/ld_example
```
Check also this: https://ldlink.nih.gov/?tab=home


## Calculate independent SNPs (Pruning)
```

./plink 	 --bfile Data/1kg_hm3_qc --maf 0.01 \
        	--indep-pairwise 50 5 0.2 \
        	--out  Results/1kg_hm3_qc_pruned
```
Select from original sample independent SNPs
```
./plink		--bfile  Data/1kg_hm3_qc \
			--extract Results/1kg_hm3_qc_pruned.prune.in \
			--make-bed \
 			--out  Results/1kg_hm3_pruned
		
		
```
## Calculate PCA
```
./plink --bfile  Results/1kg_hm3_pruned \
		--pca 10 \
		--out  Results/1kg_pca

```


## Calculate IBS and relatedness
Calculate Identity By State matrix
```
./plink --bfile  Results/1kg_hm3_pruned \
		--keep Data/1kg_samples_EUR.txt \
		--distance --out Results/ibs_matrix
```
Calculate relatedness matrix

```
./plink --bfile Results/1kg_hm3_pruned --keep Data/1kg_samples_EUR.txt --make-rel --out Results/rel_matrix
```




## Association analys

Linear additive model
```
./plink    	--bfile Data/1kg_EU_BMI \
        	--snps rs9674439 \
       	 	--assoc \
      	 	--linear \
      		--out Results/BMIrs9674439
```
Logistic additive model
```

./plink    	--bfile Data/1kg_EU_Overweight \
        	--snps rs9674439 \
       	 	--assoc \
      	 	--logistic \
      	 	--out Results/Overweight_rs9674439

```
Linear dominant analysis
```
./plink    	 --bfile Data/1kg_EU_BMI \
        	 --snps rs9674439 \
       	 	--assoc \
      	 	--linear dominant \
      	 	--out Results/BMIrs9674439
```		 
	

Genome-Wide Analysis		 
```		 
./plink    	--bfile Data/1kg_EU_BMI \
       	 	--assoc \
      	 	--linear \
      	 	--out Results/BMIgwas
```		 
