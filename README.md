# TAI-figures
Codes for reproducing figures of the TAI-seq article, organized html documents (compiled from markdown). Please follow the __html__ files to reproduce figures.

## System requirements ##
You need at least 128 GB memory, and 400 GB disk space to reproduce the analyses. 
All analyses were performed on Ubuntu 22.04.

## Software dependencies ##
All required R packages are indicated in `sessionInfo()` of each markdown file.

## Preparing codes and data ##
(1) Clone the repository with `git clone`.  
(2) Download the minimum data from BaiduNetdisk. The client for this online storage software can be downloaded from `https://pan.baidu.com/download#win`. Once you have the software installed, paste the link below into your browser to download the source data.  
```
https://pan.baidu.com/s/1y_-UiD5W2xGLSXwFo7NV1Q?pwd=TAIS
```  

## Reproducing figures ##
(1) Open any html file in the `TAI-figures` repository. All html files are organized in the same structure, so here I will use __Figure_1.html__ for demonstration. The source data (V4_data/figure_1/) has  been provided in this repo for demonstration.  
(2) In the first `Loding packages` block, change these two paths according to your environment. The `utils.R` is provided in the `TAI-figures` repository, and the `V4_data` folder is downloaded from BaiduNetdisk. So change
```
source("/media/zeemeeuw/TAI-seq3/TAI-Rmd/V4_code/utils.R")
setwd('/media/zeemeeuw/TAI-seq3/TAI-Rmd/V4_data/figure_1/')
```
into
```
source("your_path/utils.R")
setwd('your_path/V4_data/figure_1/')
```
(3) In the second `Read Seurat and ArchR object for plotting` block, change paths to theI folder containing Seurat or ArchR objects. So change
```
RDSdir <- "/media/zeemeeuw/TAI-seq/mmTAI/RNA/" # output RDS file dir
ATACdir <- "/media/zeemeeuw/TAI-seq/mmTAI/ATAC/overlapped_harmony/" # ATAC root dir
```
into
```
RDSdir <- "your_path/mmTAI/RNA/" # output RDS file dir
ATACdir <- "your_path/mmTAI/ATAC/overlapped_harmony/" # ATAC root dir
```
(4) All done! Execute block by block to reproduce all figures.  

