---
title: "Single-cell RNA"
teaching: 3h
exercises: 10
questions:
- What is a command shell and why would I use one?
objectives:
- Explain how the shell relates to the keyboard, the screen, the operating system, and users’ programs.
- Explain when and why command-line interfaces should be used instead of graphical interfaces.
keypoints:
- Many bioinformatics tools can only process large data in the command line version not the GUI.
- The shell makes your work less boring (same set of tasks with a large number of files)"
- The shell makes your work less error-prone
- The shell makes your work more reproducible.
- Many bioinformatic tasks require large amounts of computing power
---

## About this tutorial

This Single-cell RNA (scRNA) workflow is based on material from the following link:

https://satijalab.org/seurat/articles/pbmc3k_tutorial.html

10X Genomics (which makes a popular technology for single-cell sequencing), regularly publishes datasets to showcase the quality of their kits. This is commonly done on peripheral blood mononuclear cells (PBMCs), as these are a readily available source of human samples (blood) compared to other tissues. These samples also contain a set of easily distinguishable, well-annotated cell types (immune cells).

You can find more information on the dataset we will be using today here:

https://www.10xgenomics.com/datasets/3-k-pbm-cs-from-a-healthy-donor-1-standard-1-1-0

## Download and unpack the data

We will be using the filtered gene-cell matrix downloaded from the following link:

https://cf.10xgenomics.com/samples/cell-exp/1.1.0/pbmc3k/pbmc3k_filtered_gene_bc_matrices.tar.gz

Download the tarball with all the files for the expression matrix using the `wget` command.

```bash
wget https://cf.10xgenomics.com/samples/cell-exp/1.1.0/pbmc3k/pbmc3k_filtered_gene_bc_matrices.tar.gz
```

List the current working directory and see the download.

```bash
ls -F
```

```
pbmc3k_filtered_gene_bc_matrices.tar.gz
```
{: .output}

Unpack the archive with `tar`.

```bash
tar -xvf pbmc3k_filtered_gene_bc_matrices.tar.gz
```

The command `tar` will print the path of the directories, subdirectories and files it unpacks on the screen.
```
filtered_gene_bc_matrices/
filtered_gene_bc_matrices/hg19/
filtered_gene_bc_matrices/hg19/matrix.mtx
filtered_gene_bc_matrices/hg19/genes.tsv
filtered_gene_bc_matrices/hg19/barcodes.tsv
```
{: .output}

Now list the current working directory.

```bash
ls -F
```

You should see a new directory (`filtered_gene_bc_matrices/`) that was not there before. List that directory (remember to try <kbd>Tab</kbd> to autocomplete after typing a few characters of the path).

```bash
ls -F filtered_gene_bc_matrices/
```

```
hg19/
```
{: .output}

List the sub-directory under that (remember to bring back the prior command with <kbd>↑</kbd>).

```bash
ls -F filtered_gene_bc_matrices/hg19/
```

```
barcodes.tsv  genes.tsv  matrix.mtx
```
{: .output}


We now have the relative path to the directory with the files we need. We will use this path to load the data into R.

## Start an R session

## Load libraries

Load all the libraries you will need for this tutorial using the `library` command. Today we will load `dplyr`, `Seurat`, `patchwork`. 


```
library(dplyr)
library(Seurat)
library(patchwork)
```
{: .language-r}

## Read in counts and create a Seurat object.

Use the `Read10X` command to read in the downloaded counts, and `CreateSeuratObject` to create a Seurat object from this count matrix.

First, we need the path to the directory with the matrix.mtx, genes.tsv, and features.tsv files. What path is this?

```
filtered_gene_bc_matrices/hg19/barcodes.tsv
filtered_gene_bc_matrices/hg19/genes.tsv
filtered_gene_bc_matrices/hg19/matrix.mtx
```
{: .output}


Next, let’s look at the help message for Read10X.

```
?Read10X
```
{: .language-r}

If we scroll down to the examples section, we get the following:

```
data_dir <- 'path/to/data/directory'
expression_matrix <- Read10X(data.dir = data_dir)

```
{: .output}

Let’s do something similar here, but replace 'path/to/data/directory' with the appropriate path.

```
data_dir <- 'filtered_gene_bc_matrices/hg19/'
expression_matrix <- Read10X(data.dir = data_dir)
```
{: .language-r}

Next, we are going to create the Seurat object, and call it seurat_object.

From the Read10X help, the next line in the example was this:

```
seurat_object = CreateSeuratObject(counts = expression_matrix)
```
{: .language-r}

If you ran the previous step as written (creating an object called expression_matrix), you should be able to run this line as-is.


## Quality control metrics and visualization and filtering

First metric we want to look at, that needs to be calculated, is mitochondrial rates.

Use the PercentageFeatureSet argument to do this.

Generate a help message for this:

```
?PercentageFeatureSet
```
{: .language-r}

In the example at the bottom, they run the command like so to add a variable called `percent.mt` to the object, containing the mitochondrial rates.

```
pbmc_small[["percent.mt"]] <- PercentageFeatureSet(object = pbmc_small, pattern = "^MT-")
```
{: .language-r}


The pattern argument here means that we sum up the percent of reads going to all genes starting with `MT-`, e.g. `MT-ND1` and `MT-CO1`.

Let’s run this on our object, but replace `pbmc_small` with the name of the Seurat object you just made in the previous step.

```
your_seurat_object_name[["percent.mt"]] <- PercentageFeatureSet(object = your_seurat_object_name,pattern='^MT-')
```
{: .language-r}

Next, we can extract QC metrics from the object by using the `$` operator.

Besides the metric `percent.mt` we just calculated, we also get the following metrics generated automatically when we create the object:


- [nCount_RNA](#nCount_RNA) - Number of total UMIs per cell

- [nFeature_RNA](#nFeature_RNA)  -  Number of genes expressed per cell

Let’s extract all of these into a series of new objects. Syntax below for `nCount_RNA`, replace the Seurat object name with the name of your object, and the name for `nFeature_RNA` and `percent.mt` as appropriate.


```
nCount_RNA = your_seurat_object_name$nCount_RNA
```
{: .language-r}


Plot `nCount_RNA` vs. `percent.mt`


```
plot(nCount_RNA, percent.mt, cex=0.1)
```
{: .language-r}

![percent_mt plot]({{ page.root }}/fig/sc-rna-1.png)

Plot `nCount_RNA` vs. `nFeature_RNA`

```
plot(nCount_RNA, nFeature_RNA, cex=0.1)
```
{: .language-r}


![nFeature_RNA plot]({{ page.root }}/fig/sc-rna-2.png)


We find that very few cells have mitochondrial rate more than 5%, and when they do they often have very low `nCount_RNA`.

We also find that `nCount_RNA` and `nFeature_RNA` are very correlated, even at very low and very high counts. 

Based on this, don’t think we need to apply any filters on `nCount_RNA` or `nFeature_RNA`. Let’s just remove cells with mitochondrial rate more than 5% (require `percent.mt < 5`).

How do we do this? Let’s look up the `subset` function, which is actually a method for a Seurat object (so look up `SeuratObject::subset` instead of `Seurat::subset`).

```
?SeuratObject::subset
```
{: .language-r}

Syntax from the help message:

```
subset(x = seurat_object_name, subset = insert_argument_here)
```
{: .output}

Example from the help message:

```
subset(pbmc_small, subset = MS4A1 > 4)
```
{: .output}

Replace `pbmc_small` here with the name of your Seurat object, and `MS4A1 > 4` with an expression to require `percent.mt` to be less than 5.

```
your_seurat_object_name = subset(x = your_seurat_object_name, subset = your_logical_expression)
```
{: .language-r}

## Data normalization, variable feature selection, scaling, and dimensional reduction (PCA)

Next, we need to run the following steps:

1. Normalize the data by total counts, so that cells with more coverage/RNA content can be made comparable to those with less. Also log-transform. This is done using the `NormalizeData` command in Seurat.
2. Choose a subset of genes with the most variability between cells, that we will use for downstream analysis. This is done using the `FindVariableFeatures` command in Seurat.
3. Z-score (scale) the expression within each gene, for all genes in the subset from step 2. This is done using the `ScaleData` command in Seurat.
4. Run dimensional reduction (PCA) on the matrix from step 3. This is done using the `RunPCA` command in Seurat.

> ## Step summary
> - `NormalizeData`
> - `FindVariableFeatures`
> - `ScaleData`
> - `RunPCA`
{: .testimonial}

And we will just use default arguments for all of these.

> ## Running commands on an object
> Syntax to run a set of commands like this on an object, and add the results to the existing object:
> 
> ```
> your_object_name = command1(object=your_object_name)
> your_object_name = command2(object=your_object_name)
> your_object_name = command3(object=your_object_name)
> your_object_name = command4(object=your_object_name)
> ```
> {: .language-r}
>
> Replace the example syntax here with the Seurat commands, and replace the object name with the name of the object you made earlier.
>
> {: .callout}


## Selecting number of principal components (PCs) to use downstream

Next, we need to do some exploration of the results of the PCA analysis (from `RunPCA`) to decide how many principal components to use for downstream analysis.

```
ElbowPlot(object = seurat_object)
DimHeatmap(object=seurat_object, dims=1:9, cells=500, balanced=TRUE)
DimHeatmap(object=seurat_object, dims=10:13, cells=500, balanced=TRUE, ncol=2)
```
{: .language-r}

Based on the elbow plot, it looks like it would make sense to take either the first 7 or the first 10 PCs.

Based on the heatmaps, if you know immune biology, you could also make an argument for including PCs 12 and 13 (these heatmaps contain markers of rare immune cell types).

We are going to proceed with using the first 10 PCs here, following what they did in the Seurat vignette.


## Run non-linear dimensional reduction (UMAP/tSNE)

For visualization purposes, we need to be able to have a two-dimensional representation of the data. Versus PCA, where here we have 10 dimensions that we are saying all capture an important percent of the variance.

This is where non-linear techniques like tSNE and UMAP come in. Here, we will use `UMAP`, with the principal component coordinates as input.

Let’s look at the help message for the command `RunUMAP`.


```
?RunUMAP
```
{: .language-r}

Scroll down to the example.

```
# Run UMAP map on first 5 PCs
pbmc_small <- RunUMAP(object = pbmc_small, dims = 1:5)

```
{: .output}

Here, replace `pbmc_small` with the name of your Seurat object name, and run on the first 10 PCs instead of the first 5.

```
# Write your command
```
{: .language-r}

There is also a command to plot the UMAP in the example.

From the example:

```
DimPlot(object = pbmc_small, reduction = 'umap')
```
{: .output}

> ## Write your own DimPlot command
> Replace the object name with our Seurat object name.
>
> > ## Solution
> > ```
> > DimPlot(object = seurat_object, reduction = 'umap')
> > ```
> > {: .language-r}
> {: .solution}
{: .challenge}

This gives the following plot:

![UMAP plot]({{ page.root }}/fig/sc-rna-3.png)

It looks like there are probably at least 3 major clusters in this data. But right now, we have not calculated those clusters yet. We will do so in the next step.

## Cluster the cells

Seurat applies a graph-based clustering approach, which means that clustering requires two steps. First, we run the `FindNeighbors` command, which means that we calculate the distance between each cell and all other cells, and obtain the "k" nearest neighbors for each cell from these distances. Then, we use this information to obtain the clusters using the `FindClusters` command, where the "resolution" parameter tunes the number of clusters to be reported (higher resolution = more clusters).

Just like the `UMAP` command, the `FindNeighbors` command also works on the principal components (PCA) results.

```
?FindNeighbors
```
{: .language-r}

Scroll down to the example, where we see the following:


```
pbmc_small <- FindNeighbors(pbmc_small, reduction = "pca", dims = 1:10)
```
{: .output}

We want to use the first 10 PCs as input like we did for RunUMAP, so this command is almost ready to use as-is.

Just replace `pbmc_small` with the name of your Seurat object.

Insert your command here

