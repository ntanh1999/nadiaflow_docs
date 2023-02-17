Other pipelines
===============

Sometimes, you may want to run a specific part of the pipeline, not a full pipeline. 
There are two ways you can do it.

Resume pipeline 
---------------

First, if you want to re-run the analysis with some parameters changed, you can 
use the  `-resume option <https://www.nextflow.io/blog/2019/demystifying-nextflow-resume.html>`_.
It is very simple. After changing the parameters, you just need to run the **nextflow run** command again with the **-resume** option.
Nextflow will automatically figure out which part of the pipeline need to re-run and keep other jobs unchanged.

.. note:: 
    
    It is **-resume**, not **--resume**. There is only one dash.


Use different entrypoints
-------------------------

You can use different entrypoint to run a specific part of the pipeline.
To run a specific entrypoint, use the **-entry** option when running **nextflow run** command.


Reads quality control ( **-entry READS_QC**)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Steps:

- Reads quality control, trim low quality and adapter sequences (nadia-reads)
- Create barcode error plot

Input:

- Samplesheet is the same as the full pipeline.
- Sample-specific argument sheet is not required.

Output:

- Reads Quality Control html report  


Expression quantification ( **-entry QUANT**)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Steps: Sequence alignment, expression quantification, cell calling (nadia-quant)

Input:

- The samplesheet follow the same structure as that of the full pipeline. However, fastq files 
  for different lanes must be concatenated, so each sample only has one pair of fastq files. 

- Argument sheet has the same structure as that of the full pipeline. But, only nadia-quant columns will be used.

Output:

- Cell quality control html report
- Matrix in h5ad and mtx format

Matrix processing ( **-entry PROCESS**)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Steps: 

- Data processing of gene expression matrix (nadia-process)
- Combine multiple datasets (nadia-combine)

Input:

Samplesheet has to be a comma-separated file with 2 columns, and a header row as shown in the examples below.

.. csv-table::
    :header: Column,Description

    sample,Custom sample name. Spaces are not allowed in sample name.
    h5ad,Full path to gene expression matrix in h5ad format.


Argument sheet has the same structure as that of the full pipeline. But, only nadia-process columns will be used.

Output:

- Matrix processing html report with many plots
- Processed matrix in h5ad
- Combine sample matrix in h5ad format