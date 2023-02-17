Full pipeline
=============

Overview
--------

The full pipeline perform following steps:

1. Reads quality control, trim low quality and adapter sequences (nadia-reads)
2. Create barcode error plot
3. Sequence alignment, expression quantification, cell calling (nadia-quant)
4. Data processing of gene expression matrix (nadia-process)
5. Combine multiple datasets (nadia-combine)

The full pipeline is run by default, or using FULL entrypoint (**--entry FULL**)


Input
-----

1. Prepare samplesheet
~~~~~~~~~~~~~~~~~~~~~~

You will need to create a samplesheet with information about the samples you 
would like to analyse before running the pipeline. You could use **--input** 
parameter to specify the location of samplesheet:

.. code-block:: bash

    --input '[path to samplesheet file]'



Samplesheet has to be a comma-separated file with 3 columns, and a header row as shown in the examples below.

.. csv-table::
    :header: Column,Description

    sample,Custom sample name. This entry will be identical for multiple sequencing libraries/runs from the same sample. Spaces are not allowed in sample name.
    fastq_1,Full path to FastQ file for Illumina short reads 1. File has to be gzipped and have the extension ".fastq.gz" or ".fq.gz".
    fastq_2,Full path to FastQ file for Illumina short reads 2. File has to be gzipped and have the extension ".fastq.gz" or ".fq.gz".


.. note::

    One sample could have multiple runs/libraries e.g. to increase sequencing 
    reads. In this case, the sample identifiers have to be the same. 
    Below is an example for the same sample sequenced across 2 lanes. The 
    pipeline will concatenate the raw reads before performing any downstream analysis.

    .. code-block::
    
        sample,fastq_1,fastq_2
        S1,s3://tower-nadiaflow-test/minimal_test/data/S1_L1_R1.fastq.gz,s3://tower-nadiaflow-test/minimal_test/data/S1_L1_R2.fastq.gz
        S1,s3://tower-nadiaflow-test/minimal_test/data/S1_L2_R1.fastq.gz,s3://tower-nadiaflow-test/minimal_test/data/S1_L2_R2.fastq.gz


You can download an :download:`example samplesheet </_static/sheet/minimal_test_input.csv>` 

2. Prepare argument sheet
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When running the pipeline, arguments between samples could be different. E.g. 
the number of cells to keep could be different between samples. We can provide
sample-specific argument by a comma-separated file. You could use **--args** 
parameter to specify the location of the argument sheet:

.. code-block:: bash

    --args '[path to argument sheet file]'


The argument sheet could have many columns (listed bellow). **sample** column is 
required and unique. Each column is an argument, taken from nadiatools 
(*nadia-quant* and *nadia-process*). These arguments will be used to run nadia 
commands. If a column is not specified or argument value is null, then default 
value will be used.

nadia-quant options:

.. csv-table::
    :header: Column,Type,Description,Default

    top_cells,integer,Sort the barcodes in the descending order of count (UMI/reads) and keep the first barcodes.,
    expect_cells,integer,Use the expected number of cells as a hint to estimate a robust cutoff around this value.,
    emptydrops_cr,boolean,"If true, then use EmptyDrops implementation from Cell Ranger for cell calling. Only available for STARsolo pipeline.",false
    knee,boolean,"If true, use the method in the whitelist command of UMI-tools to automatically determine the number of true barcodes. Only available for Alevin-fry pipeline.",false
    mixed_species,boolean,"If true, output doublet rate and barnyard plot for mixed human and mouse sample.",false
    ratio,number,The threshold to classify human and mouse.,0.8



nadia-process options:

.. csv-table::
    :header: Column,Type,Description,Default

    max_mito,number,Maximum percentage of UMIs derived from mitochondrial genes,0.2
    min_ribo,number,Minimum percentage of UMIs derived from ribosomal genes,0.05
    min_gene,integer,Minimum number of gene count,200
    max_gene,integer,Maximum number of gene count,
    min_umi,integer,Minimum number of UMI count,
    max_umi,integer,Maximum number of UMI count,
    filter_doublet,boolean,"If true, filter out predicted doublets",false
    expected_rate,number,Expected doublet rate,0.06
    min_doublet_score,number,"Minimum doublet score. If not specified, automatically estimate the threshold.",
    min_cell,integer,Filter genes by number of cells,
    remove_mito,boolean,"If true, remove mitochondrial genes",false
    remove_ribo,boolean,"If true, remove ribosomal genes",false
    remove_genes,string,"List of genes to remove (gene symbols)",
    regress,string,"List of variable to regress. Options: n_counts, n_genes, Mito_percent,…","n_counts Mito_percent"
    n_pcs,integer,Number of Principle Components to compute UMAP and tSNE.,30
    n_neighbors,integer,Number of neighbors to compute UMAP.,20
    plot_genes,string,List of genes of interest to plot.,


Here is an example argument sheet:

.. code-block:: bash

    sample,top_cells,max_mito,min_ribo,min_gene,min_umi,filter_doublet,min_cell,n_pcs,plot_genes
    S1,1000,1,0,0,0,false,0,10,MALAT1 mt-Atp8 MT-CO2
    S2,1000,1,0,0,0,false,0,10,MALAT1 mt-Atp8 MT-CO2
    S3,1000,1,0,0,0,false,0,10,MALAT1 mt-Atp8 MT-CO2



You can download an :download:`example argument sheet </_static/sheet/minimal_test_args.csv>` 

Usage examples
--------------

After preparing samplesheet and argument sheet, you can pull and launch nadiaflow pipeline using the following commands:

.. code-block:: bash

    nextflow pull 'https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/nadiaflow'

    nf-core launch ~/.nextflow/assets/codecommit-ap-northeast-1/nadiaflow
