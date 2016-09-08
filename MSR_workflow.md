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

    see Pat Schloss' SOP for MiSeq data (http://www.mothur.org/wiki/MiSeq_SOP)
* [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/))
* Access to HPC or high RAM computer

##Workflow
----------

This data can be used for either the 18S or 16S rRNA gene sequences. For this documentation it is written out for the 16S rRNA gene data. Any changes that would be different for 18S rRNA gene data will be denoted in the text.
### Download the data

1. Download the MSR data from [NCBI SRA](http://www.ncbi.nlm.nih.gov/sra)
### Run Mothur

2. Create a .files file for *Mothur* analysis

    *  Example:
        A1	A1_CTACAGGGTCTC_L001_R1_001.fastq	A1_CTACAGGGTCTC_L001_R2_001.fastq
        A1Prime	A1Prime_CTTGGAGGCTTA_L001_R1_001.fastq	A1Prime_CTTGGAGGCTTA_L001_R2_001.fastq
        B1	B1_ACCGAACAATCC_L001_R1_001.fastq	B1_ACCGAACAATCC_L001_R2_001.fastq
        B1Prime	B1Prime_ACGGTACCCTAC_L001_R1_001.fastq	B1Prime_ACGGTACCCTAC_L001_R2_001.fastq
3. Create a mothur batch file

    **Provided**: *msriver_16S.batch*
4. Run *Mothur* using the batch file and the .files file you created

    *  `mothur msriver.batch`
    *  Analysis of each MSR dataset (16S and 18S) will take 3-5 days to run on 250 GB RAM with 16 processors
5. Move the following files to your personal computer or where you are working

    1) *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.shared 2) *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.0.03.cons.taxonomy
### Prepare data for analyses

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
    *  **Be careful** to double check this. You will not be able to open this file in excel because of its size!!!
    *  Remove OTU # from the columns

    **Provided**: *MSR_allOTUs_16S.csv*

7. Create a table with your sample data (*e.g. nutrient measurements, site details, filter details*) that has been [Hellinger transformed](http://cc.oulu.fi/~jarioksa/softhelp/vegan/html/decostand.html).

    **Provided**: *MSR_allNUT.csv*
8. Create a taxonomy file from the *.files file name*.trim.contigs.good.unique.pick.good.filter.unique.precluster.pick.pick.an.unique_list.0.03.cons.taxonomy file
    *  Remove the OTU # column and separate the taxonomy into separate columns.
    *  File should just be Kingdom - Genus if Silva Database was used.

    **Provided**: *MSR_allTaxa_16S.csv*

### First look and removing outlier OTUs and sites

8. Moving to R studio, look at all the data (*e.g. sites, controls*) using NMDS and hierarchical clustering.

    **Provided**: *Alldata_16S.R*
9. Based on the hclust tree  and NMDS plot remove any outlier sites that group with the negative and cooler controls in both the tree and NMDS plot.
10. Remove any OTUs  found within the Negative and Cooler controls that appear >= 10 at any given site.
11. Update taxonomy file to remove any OTUs that were removed in the above analysis

    *  *IMPORTANT*: The taxonomy file must match the OTU file
12. Update sample sheet to remove any samples that were outliers


### Phyloseq

13. Moving back to R studio, we use the package [Phyloseq](https://joey711.github.io/phyloseq/) with the code *MSR_phyloseq_16S.R* to analyze the data

    *  Within the code are the dependencies and other programs needed to run *Phyloseq* and plot the data.
    *  You will need to upload your Tax, Nut, and OTU tables into R studio. *Note*: NUT table is **required** by phyloseq even if you do not have nutrient data.

    **Provided**: *MSR_phyloseq_16S.R*
14. Within Phyloseq, once the data has been normalized with [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html) and separated by size fraction, you will need to write out a table of your taxonomy, OTU, and NUT for the sterivex and prefilter.

### WGCNA

15. Modify the Taxonomy table so that each classification is separated by a colon

    **Example**: Bacteria(100);Proteobacteria(100);Betaproteobacteria(100);Methylophilales(100);Methylophilaceae(100);unclassified(80)
16. Upload the Taxonomy, Nutrient, and OTU tables into *Rstudio* to be processed with the WGCNA and VIP script

    *  Within the scripts there are dependencies listed that you will need to download visit the [WGNCA website](https://labs.genetics.ucla.edu/horvath/CoexpressionNetwork/Rpackages/WGCNA/) for more information.

17. Create a table with the WGCNA and VIP output

    *  To do this, first, use the OTUinfo table created. Find the submodule of choice and copy and paste the OTUs, their classification, P value, and R value for the metadata of choice (e.g. NO3).
    *  Next, in the VIP values file for the metadata of choice (e.g. NO3) copy and paste the VIP value with its corresponding OTU in your new excel table. **NOTE**: the VIP score and OTU table from the previous step will not be in the same order you *will* need to reorder them so that they match.
    *  Using the Nodeconnections file for the metadata of choice (e.g NO3), copy and paste the number of connections into the new excel file. *Again*, you will need to make sure that the OTUs match.
