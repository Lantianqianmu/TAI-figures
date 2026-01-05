# TAI-figures
Codes for reproducing figures of the TAI-seq article, organized in R markdown files and html documents (compiled from markdown).

## System requirements ##
The source data is too large to be uploaded onto github, and is available from lead contact. You need at least 128 GB memory, and 200 GB disk space to reproduce the analyses. 
We performed all analyses on Ubuntu 22.04.

## Software dependencies ##
Dependencies  | Version
------------- | -------------
R | 4.0.2
Seurat | 4.4.0
ArchR | 1.0.2

Other R packages are indicated in sessioninfo of each markdown file.

## Reproducing figures ##
(1) Clone the repository with `git clone`, and execute
```
cd TAI-figures
```
(2) Prepare source data. There will be two set of data: a) the V1_data folder, which stores tsv/excel files, and b) R object folders, which contain processed ArchR and Seurat project. 
(3) Please set the correct path to source data, and path to ___utils.R___ at the beginning of each markdown.  
(3) Execute the code block-by-block to reproduce figures. Time to reproduce each figure varies from seconds to 30 minutes.  






