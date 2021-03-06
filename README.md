# Hypergraph for Dynamic Correlation

R code for methods proposed by https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-019-5787-x.

## Prerequisites

The following packages are required for executing the main code file: igraph, coin, ks, dynamicTreeCut, GOstats. For the sample dataset in the below example, org.Sc.sgd.db is also needed.

## Usage

### Data formats

* A gene expression matrix (called "array"), seen in spellman_73_filled.bin for this example
* A list of GO gene module selection (called "GO.select"), seen in GO_select_yeast_ 0.7 0.4 1000 20 .bin for this example

### Preliminary process

Suposse all relevant files are in the same folder, and the work directory has been set properly. First, load in data (as demonstration, we only random sample 1000 genes here)
```
load('spellman_73_filled.bin')
load("GO_select_yeast_ 0.7 0.4 1000 20 .bin")
array <- array[sample(nrow(array), 1000),]
```
and load the main code file
```
source("main.r")
```
#### NOTE: The program is running on a Windows machine by default. 
If you are using other operating systems, in main.r, find and comment out the line
```
dyn.load("csupp.dll") 
```
and uncomment the line 
```
dyn.load("csupp.so")
```
for Linux, or uncomment the line
```
dyn.load("csupp_OS/csupp.so")
```
for Mac OS.

Relevant files are all provided in this repository.

### Unsupervised hypergraph construction

To construct module-level hyergraph, use
```
hp_us <- hypergraph_unsup(array, fdr=0.2, save_glist=T)
```
and visualize the full hypergraph and top-connected sub-hypergraph by
```
full_hypergraph_us <- plot_entrie_unsup(hp_us$net, hp_us$label, folds=2)
plot_top_unsup(full_hypergraph_us$g, full_hypergraph_us$elist, hp_us$label, full_hypergraph_us$folds)
```
(Figure1 and Figure2)

One can also conduct GO enrichment test using
```
enrichment <- GOen(hp_us$glist, hp_us$label)
```
which generates a list of enrichment results for each gene cluster.

### Supervised hypergraph construction

To construct module-level hyergraph, use
```
hp_sp <- hypergraph_sup(array, GO.select, fdr=0.2, save_triplets=T, save_glist=T)
```
and visualize the full hypergraph, top-connected sub-hypergraph and module-specified sub-hypergraph by
```
full_hypergraph_sp <- plot_entrie_sup(hp_sp$net, hp_sp$label, GO.select, folds=10)
plot_top_sup(full_hypergraph_sp$g, full_hypergraph_sp$elist, hp_sp$label, GO.select, full_hypergraph_sp$folds)
plot_one_sup(full_hypergraph_sp$g, full_hypergraph_sp$elist, hp_sp$label, GO.select, full_hypergraph_sp$folds, 
             "GO:0006090", max_num_display = 16)
```
(Figure3, Figure4 and Figure5)

Suppose one is interested in the gene level hypergraph given a specific hyperedge:
```
GO <- names(GO.select)
module_names <- Term(GO)
which(GO == "GO:0006090")
which(GO == "GO:0045937")
which(GO == "GO:2000241")
hyperedge <- c(232, 141, 1)

plot_gene_level(hyperedge, GO, hp_sp$net, hp_sp$label, hp_sp$triplets, hp_sp$glist, folds=10)

```
(Figure6)

