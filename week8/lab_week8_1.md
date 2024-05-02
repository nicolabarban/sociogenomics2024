# Lab week 8. Working with summary statistics

## Manhattan Plots, QQplots and genetic correlations  matrices
## Alternative plots using package manhattanly

This first part of the lab will use Rstudio. 

Use R to download the summary statistics
```
download.file("http://ssgac.org/documents/EduYears_Main.txt.gz", dest="EA2_results.txt.gz")
```


 Import the summary statistics in R
```
EAgwasResults<-read.table("EA2_results.txt.gz", header=T)

head(EAgwasResults)
dim(EAgwasRsults)
```

This is a very large file, we can speed up things by selecting ony SNPS with Pvalue <0.005
```
EAgwasResults_sub<-subset(EAgwasResults, Pval<0.0005)
dim(EAgwasResults_sub)

```



We can use a library created to plot manhattan plots
Load the manhattanly library
```
install.packages("manhattanly")
library(manhattanly)
```

Create Manhattan plot 
```
help(manhattanly)
manhattanly(EAgwasResults_sub, snp = "MarkerName" , bp="POS", p="Pval", chr="CHR")
```



We can use the same library to plot QQplots
```
qqly(EAgwasResults, snp = "MarkerName" , bp="POS", p="Pval", chr="CHR")

```

## Plotting genetic correlations  in R
import data on genetic correlation

```

data_rg<-read.table("http://nicolabarban.com/sociogenomics2023/week8/LD-Hub_genetic_correlation_example.txt",fill =T, sep="\t", header=T, quote="") 
```



draw heatmap
```
install.packages("ggplot2")
library(ggplot2)
ggplot(data = data_rg, aes(Trait1, Trait2, fill = rg))+
    geom_tile(color = "white")+
    scale_fill_gradient2(low = "blue", high = "red", mid = 
                                 "white",  midpoint = 0, limit = 
            c(-1.1,1.1), space = "Lab",
              name="Genetic\nCorrelation") +
      theme_minimal()+ 
    theme(axis.text.x = element_text(angle = 45, vjust = 1, 
          size = 8, hjust = 1))+
 coord_fixed()
```

