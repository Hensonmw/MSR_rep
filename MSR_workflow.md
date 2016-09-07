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
* Your favorite text editor (We use [atom](https://atom.io/))
* [Mothur](http://www.mothur.org/)
* Check the data for quality (We use [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/))

    see Pat Schloss' SOP for MiSeq data (http://www.mothur.org/wiki/MiSeq_SOP)
* Access to HPC or high RAM computer

##Workflow
----------

1. Download the MSR data from [NCBI SRA](http://www.ncbi.nlm.nih.gov/sra)
2. Create a .files file for *Mothur* analysis
*  Example:

    A1	A1_CTACAGGGTCTC_L001_R1_001.fastq	A1_CTACAGGGTCTC_L001_R2_001.fastq
    A1Prime	A1Prime_CTTGGAGGCTTA_L001_R1_001.fastq	A1Prime_CTTGGAGGCTTA_L001_R2_001.fastq
    B1	B1_ACCGAACAATCC_L001_R1_001.fastq	B1_ACCGAACAATCC_L001_R2_001.fastq
    B1Prime	B1Prime_ACGGTACCCTAC_L001_R1_001.fastq	B1Prime_ACGGTACCCTAC_L001_R2_001.fastq
3. Create a mothur batch file

    Provided: *msriver.batch*
4. Run *Mothur* using the batch file and the .files file you created
*  'mothur msriver.batch'
*  Analysis of each MSR dataset (16S and 18S) will take 3-5 days to run on 250 GB RAM with 16 processors
5. Move the following files to your computer
*  1) *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.shared 2) *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.0.03.cons.taxonomy
6. Transpose the *.shared* file:
```
awk '
{
    for (i=1; i<=NF; i++)  {
        a[NR,i] = $i
    }
}
NF>p { p = NF }
END {   
    for(j=1; j<=p; j++) {
        str=a[1,j]
        for(i=2; i<=NR; i++){
            str=str" "a[i,j];
        }
        print str
    }
}' *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.shared > output.txt
```
*  You can also do this within R studio with the command `t(otu_table)`
*  Be careful to double check this. You will not be able to open this file in excel because of its size!!!
* Remove OTU # from the columns

    Provided: *MSR_allOTUs.csv*
7. Create a table with your sample data (*e.g. nutrient measurements, site details, filter details*)

    Provided: *MSR_nut.csv*
8. Create a taxonomy file from the *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.0.03.cons.taxonomy file
    *  Remove the OTU # column and separate the taxonomy into separate columns.
    *  File should just be Kingdom - Genus if Silva Database was used.

    Provided: *MSR_allTaxa.csv*
8. Moving to R studio, look at all the data (*e.g. sites, controls*) using NMDS and heiracrical clustering.

    Provided: *Alldata.R*
9. Based on the hclust tree  and NMDS plot remove any outlier sites that group with the negative and cooler controls in both the tree and NMDS plot.
10. Remove any OTUs  found within the Negative and Cooler controls that appear >= 10 at any given site.

    Provided: *MSR_OTUfinal.csv*
11. Update taxonomy file to remove any OTUs that were removed in the above analysis
    *  *IMPORTANT*: The taxonomy file must match the OTU file

    Provided: *MSR_Taxafinal.csv*
12. Update sample sheet

    Provided: *MSR_NUTfinal.csv*
13. Moving back to R studio, Use the package [Phyloseq](https://joey711.github.io/phyloseq/) and the code *MSR_phyloseq.R* to analyze the data 
