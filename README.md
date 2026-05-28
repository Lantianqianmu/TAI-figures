# TAI-figures
Codes for reproducing figures of the TAI-seq article, organized in R markdown files and html documents (compiled from markdown). Please follow the __html__ files to reproduce figures.

## System requirements ##
You need at least 128 GB memory, and 400 GB disk space to reproduce the analyses. 
All analyses were performed on Ubuntu 22.04.

## Software dependencies ##
All required R packages are indicated in `sessionInfo()` of each markdown file.

## Preparing codes and data ##
(1) Clone the repository with `git clone`.  
(2) Download the source data from BaiduNetdisk. The client for this online storage software can be downloaded from `https://pan.baidu.com/download#win`. Once you have the software installed, click the link below to download the zipped source data. The data can be downloaded/unzipped into anywhere you desire.  
(3) Unzip __TAI-seq.zip__, which contains 6 subfolders:  
`mmTAI`: RNA (Seurat RDS files), ATAC (ArchR RDS and arrow files) and TCR (xlsx or tsv files) input files of the mouse LCTA.
`mmTAI_d8sp`, `mTEC`, `mouse_skin` and `pituitary`: Chromatin potential of public datasets (figure S11-S12).  
`V2_data`: Other source data.

## Reproducing figures ##
(1) Open any html file in the `TAI-figures` repository. All html files are organized in the same structure, so here I will use __Figure_3.html__ for demonstration.  
(2) In the first `Loding packages` block, change these two paths according to your environment. The `utils.R` is provided in the `TAI-figures` repository, and the `V2_data` folder is in __TAI-seq.zip__. So change
```
source("/media/zeemeeuw/TAI-seq/TAI-Rmd/V2_code/utils.R")
setwd('/media/zeemeeuw/TAI-seq/TAI-Rmd/V2_data')
```
into
```
source("your_path/utils.R")
setwd('your_path/V2_data')
```
(3) In the second `Read Seurat and ArchR object for plotting` block, change paths to folders according to your environment. Here the `mmTAI` folder is provided in __TAI-seq.zip__, and therefore you only need to change the root dir. So change
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

