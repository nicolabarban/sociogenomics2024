# Lab 2. Getting started with Plink

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.png)](https://ssh.cloud.google.com/cloudshell/open?cloudshell_git_repo=https://github.com/nicolabarban/sociogenomics2024&cloudshell_tutorial=week5/lab5.md)





## Installing Plink in your system

**Let's start from our home directory and change directory to** `Sociogenomics/Software`
```
cd $HOME
cd Sociogenomics/Software
```

PLINK is available from [here:](https://www.cog-genomics.org/plink/)
We use (for now) PLINK 1.9. Now we can download the linux version in our system

```
wget https://s3.amazonaws.com/plink1-assets/plink_linux_x86_64_20230116.zip
unzip plink_linux_x86_64_20230116.zip
```

let's check file permission. We need to make plink executable to use it as software
```
ls -l
```

Check file permissions
* chmod +rwx filename to add permissions.
* chmod -rwx directoryname to remove permissions.
* chmod +x filename to allow executable permissions.
* chmod -wx filename to take out write and executable permissions.



This is how we make the file executable
```
chmod +x plink
```

we can now execute the software using `./` in front of the file
```
./plink --help 

```

### Create symbolic links


```
cd $HOME/Sociogenomics
ln -s Software/plink
```

Now we can access PLINK from this directory

```
pwd
./plink --help 
```

## How to read PLINK files

![data type](Fig_7_3.jpg)


### Read Binary PLINK file

we start with PLINK binary files 


1. `.bim` file  contains info on the markers
```
head Data/hapmap-ceu.bim
```
2. `.fam` file  contains info on the individuals 
```
head Data/hapmap-ceu.fam
```

2. `.bed` files are not readable!
```
head Data/hapmap-ceu.bed
```
### Recode PLINK file


Recode into map and ped files
```
./plink --bfile Data/hapmap-ceu --recode --out Results/hapmap-ceu
```

1. `.map` file  contains info on the markers

```
head Results/hapmap-ceu.map
```

2. `.ped` file  contains info on the individual genotypes
```
head -1 Results/hapmap-ceu.ped
```

# Part II. Descriptive Statistics

### Allele frequency
We can calculate allele frequency

```
 ./plink --bfile Data/c --freq --out Results/Allele_Frequency
```

```
head Results/Allele_Frequency.frq 
```

### Missing values

individuals
```

./plink --bfile Data/1kg_hm3 --missing --out Results/missing_data

```
variants
```

head Data/missing_data.imiss
```


how many observations?

```
 wc -l Data/1kg_hm3.fam
```

how many variants?

```
 wc -l Data/1kg_hm3.bim
```




## Quality control

Missing individuals rate
```
./plink --bfile Data/1kg_hm3 \
		--mind 0.05 \
		--make-bed \
		--out Results/1kg_hm3_mind005
```


Calculate heterozygocity
```

./plink --bfile Data/1kg_hm3 \
		--het --out Results/1kg_hm3_het
```


Low call-rate SNPS
```

./plink --bfile Data/1kg_hm3 \
		--geno 0.05 \
		--make-bed \
		--out Results/1kg_hm3_geno
```

Allele frequency
```

./plink --bfile Data/1kg_hm3 \
	 	--maf 0.01 \
		--make-bed  --out Results/1kg_hm3_maf
		
```
deviation from HWE
```

./plink --bfile Data/1kg_hm3 \
	 	--hwe 0.00001 \
		--make-bed  --out Results/1kg_hm3_hwe

```

```
./plink     --bfile Data/1kg_hm3 \
       	--mind 0.03 \
       	--geno 0.05 \
       	--maf 0.01 \
    	--hwe 0.00001 \
        --make-bed  --out Data/1kg_hm3_QC      
			
```




Calculate linkage disequilibrium
```
./plink --bfile Data/1kg_hm3 \
	 	--ld rs1048488 rs3115850 \
		--out Results/ld_example
```



./plink 	 --bfile Data/1kg_hm3_QC \
        	--indep-pairwise 50 5 0.2 \
        	--out  Results/1kg_hm3_QC_pruned
```
Select from original sample independent SNPs
```
./plink		--bfile  Data/1kg_hm3 \
			--extract Results/1kg_hm3_QC_pruned.prune.in \
			--make-bed \
 			--out  Results/1kg_hm3_QC_pruned
		
		
```
## Calculate PCA
```
./plink --bfile  Results/1kg_hm3_QC_pruned \
		--pca 10 \
		--out  Results/1kg_pca

```

---
**This part needs to be exectuted in R**

### Panel A
```
library(ggplot2)
columns=c("fid", "Sample.name", "pca1", "pca2", "pca3", "pca4",   "pca5", "pca6", "pca7", "pca8", "pca9", "pca10")

pca<- read.table(file="1kg_pca.eigenvec", sep = "", header=F, col.names=columns)[,c(2:12)]

ggplot(pca, aes(x=pca1, y=pca2))+ geom_point()+ theme_bw()+  xlab("PC1") + ylab("PC2")
```


#### Panel B
```
geo <- read.table(file="1kg_samples.txt", sep = "\t",header=T)[,c(1,4,5,6,7)]


data <- merge(geo, pca, by="Sample.name")
ggplot(data, aes(x=pca1, y=pca2, col=Superpopulation.name))+
     	 geom_point()+ theme_bw()+
     	 xlab("PC1") + ylab("PC2")+
 	 labs(col = "")
```
---


## Add Phenotype
```
./plink     --bfile Data/1kg_hm3_QC \
			--pheno Data/height_sim.phen \
			--make-bed \
			--keep Data/1kg_samples_EUR.txt \
			--out  Data/1kg_height_EUR
```




## Association analys

Linear additive model
```
./plink    	--bfile Data/1kg_height_EUR \
        	--snps rs9674439 \
       	 	--assoc \
      	 	--linear \
      		--out Results/HEIGHTrs9674439
```


Linear dominant analysis
```
./plink    	 --bfile Data/1kg_height_EUR \
        	 --snps rs9674439 \
       	 	--assoc \
      	 	--linear dominant \
      	 	--out Results/HEIGHTrs9674439
```		 
	

Genome-Wide Analysis		 
```		 
./plink    	--bfile Data/1kg_height_EUR \
       	 	--assoc \
      	 	--linear \
      	 	--out Results/HEIGHTgwas
```		 



### Genome-Wide Analysis	with covariates	 
```		 
./plink    	--bfile Data/1kg_height_EUR \
       	 	--assoc \
      	 	--linear \
			--covar Results/1kg_pca.eigenvec \
      	 	--out Results/HEIGHTgwas_cov
```		 
