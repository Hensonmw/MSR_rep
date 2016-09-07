#Analyzing the Mississippi River Data
======================================

* Recreate workflow used to analyze the 16S and 18S rRNA gene survey of the Mississippi River
* Data was collected during the Fall of 2013

##Requirements
--------------

In order to recreate the work you will need to install the following programs:
* [R Studio](https://www.rstudio.com/)
* [Excel](https://office.live.com/start/Excel.aspx)
* Commandline terminal
* Your favorite text editor
* [Mothur](http://www.mothur.org/)
    see Pat Schloss SOP for MiSeq data (http://www.mothur.org/wiki/MiSeq_SOP)
* Access to HPC or high RAM computer

##Workflow
----------

1) Download the MSR data from [NCBI SRA](http://www.ncbi.nlm.nih.gov/sra)
2) Create a .files file for Mothur analysis
    Example:
        A1	A1_CTACAGGGTCTC_L001_R1_001.fastq	A1_CTACAGGGTCTC_L001_R2_001.fastq
        A1Prime	A1Prime_CTTGGAGGCTTA_L001_R1_001.fastq	A1Prime_CTTGGAGGCTTA_L001_R2_001.fastq
        B1	B1_ACCGAACAATCC_L001_R1_001.fastq	B1_ACCGAACAATCC_L001_R2_001.fastq
        B1Prime	B1Prime_ACGGTACCCTAC_L001_R1_001.fastq	B1Prime_ACGGTACCCTAC_L001_R2_001.fastq
3) Create a mothur batch file
    Provided: *msriver.batch*
4)
