Local installation
==================

1. Install nextflow
-------------------

See: https://www.nextflow.io/docs/latest/getstarted.html#installation

Run the following command to install nextflow:

.. code-block:: bash

    # Make sure that Java v8+ is installed:
    java -version

    # Install Nextflow
    curl -fsSL get.nextflow.io | bash

    # Add Nextflow binary to your user's PATH:
    sudo mv nextflow /usr/local/bin


Nextflow could be install by conda:

.. code-block:: bash

    conda install -c bioconda nextflow


2. Install nf-core
------------------

See: https://nf-co.re/docs/usage/tutorials/nf_core_usage_tutorial#installing-the-nf-core-helper-tools

By pip:

.. code-block:: bash

    pip install nf-core

By conda:

.. code-block:: bash

    conda install nf-core


3. Add Crecidentials
--------------------

To run nadiaflow pipeline, we need to have access to nadiaflow repository and nadiatools docker image.

- The code of **nadiaflow** and **nadiatools** is stored in AWS CodeCommit repository (ap-northeast-1 region).
- The docker image of **nadiatools** is pushed to AWS Elastic Container Registry (ap-northeast-1 region). 

You need to have an AWS IAM user to have access to these repositories.

Now, assume that you have *accessKey* and *secretKey* of AWS IAM user with proper permission.


Go to **.nextflow** folder in home directory. And create two text files **scm** and **config** with your keys.

.. code-block:: bash

    cd ~/.nextflow

    touch scm

    touch config



config file: 

.. code-block:: bash

    aws {
        accessKey = 'accessKey'
        secretKey = 'secretKey'
        region = 'ap-northeast-1'
    }

csm file:

.. code-block:: bash

    providers {

        aws_repo {
            platform = 'codecommit'
            user = 'accessKey'
            password = 'secretKey'
        }

    }

4. Pull the pipeline
--------------------

Now, we can pull nadiaflow pipeline to local machine.

.. code-block:: bash

    nextflow pull 'https://git-codecommit.ap-northeast-1.amazonaws.com/v1/repos/nadiaflow'
