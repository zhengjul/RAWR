RAWR is a Python program intended as a non-parametric resampling method to rival the classic bootstrap resampling. RAWR and SERES are sequential resampling algorithms for support estimation of multiple sequence alignments and phylogenetic tree alignments. RAWR and SERES conceptually work by random walking along biological molecular sequences and retaining the sequence dependence during the resampling process. We present this tutorial for people who prefer to use a GUI application or a website interface. The algorithms are freely available under the GNU General Public License.

* [Installation](#installation)
 * [Software Install](#software-installation)
 * [Website Install](#website-installation)
 * [API Install](#API-installation)
  * [Running RAWR](#running-rawr)
    * [Sample sequences](#sample-sequences)
      * [**Basic usage**](#basic-usage)
      * [**Sample more sequences**](#sample-more-sequences)
      * [**Different reverse rate**](#different-reverse-rate)
      * [**SERES resampler**](#seres-resampler)
    * [Estimate support](#estimate-support)
      * [**MSA support estimation**](#msa-support-estimation)
      * [**Tree support estimation**](#tree-support-estimation)
    * [Running on large dataset](#running-on-large-dataset)
  * [Output](#output)
    * [sample sequence output](#sample-sequence-output)
    * [MSA support estimation output](#msa-support-estimation-output)
    * [Tree support estimation output](#tree-support-estimation-output)

  * [Running RAWR](#running-rawr)
    * [Sample sequences](#sample-sequences)
      * [**Basic usage**](#basic-usage)
      * [**Sample more sequences**](#sample-more-sequences)
      * [**Different reverse rate**](#different-reverse-rate)
      * [**SERES resampler**](#seres-resampler)
    * [Estimate support](#estimate-support)
      * [**MSA support estimation**](#msa-support-estimation)
      * [**Tree support estimation**](#tree-support-estimation)
    * [Running on large dataset](#running-on-large-dataset)
  * [Output](#output)
    * [sample sequence output](#sample-sequence-output)
    * [MSA support estimation output](#msa-support-estimation-output)
    * [Tree support estimation output](#tree-support-estimation-output)

Installation
---------------

<!-- - Web server: The users can directly use the web server for small dataset analysis. The limitation of the web server is 50Mb of the input alignment with no more than 50 taxa.
- Mac OS GUI client: To install Mac OS GUI client locally, you need to download the installation package from [TODO].
Then double click the installation package and install the software on your device.  -->
Use ```pip install RAWR``` to install the python module. Python 3.6 or later version is recommended. Other required dependencies and version requirement shows as following.  
  - MAFFT v7 or later version. Download here https://mafft.cbrc.jp/alignment/software/.
  - RAxML version 7.2.8 or later version. Download here https://github.com/stamatak/standard-RAxML.

## **Running RAWR**
After install the RAWR module, you can sample sequences and calculate MSA support or phylogenetic support by Python script. 

RAWR also have web server and Mac OS GUI client.
Access to web server by this link [TODO: web server link].
The limitation of the web server is 50Mb of the input alignment with no more than 50 taxa.
You can download and install the Mac OS GUI here [TODO: software installation package].

<!-- ### Web server
Access to the web server by [TODO: web server address]. Upload the input alignment file, select the parameters and click 
### MAC OS GUI software -->
### **Sample sequences**
First download this [alignment file](exampleData/alignment.fasta) to your device.
 <!-- and [phylogenetic tree file](exampleData/infer.tree).  -->

Copy the downloaded files to the directory that you would like to save the analysis results and sampled sequences. 
```bash
cp $downloadDir/alignment.fasta $workDir/
```
Then start a interactive Python environment or open a Python script. 

#### **Basic usage**

Following code creates an instance of rawrSampler with default *reverseRate* and *samplenum*.
It will generate 10 sampled sequences with 0.1 reverse rate. 
The sampled sequences will be saved to *ABSOLUTE_PATH_OF_WORKDIR/samples/*

```python
>>> import RAWR
>>> alnFile = "ABSOLUTE_PATH_OF_WORKDIR/alignment.fasta"
>>> outputDir = "ABSOLUTE_PATH_OF_WORKDIR/"
>>> rawr = RAWR.sampler.rawrSampler(alnFile, outputDir)
>>> rawr.sampleSeqs()
```
![Sampled sequences](figure/sampledSequences.png)

#### **Sample more sequences**

The default sample number is 10. You can sample more sequences by create a new *rawrSampler* instance with a larger *samplenum*. 
Following code shows an example that creates 50 sampled sequences for input alignment. 

```python
>>> rawr = RAWR.sampler.rawrSampler(alnFile, outputDir, samplenum=50)
>>> rawr.sampleSeqs()
```

#### **Different reverse rate**

The default reverse rate is 0.1. 
By assign a different value to *reverseRate*, you can change the reverse rate of the RAWR random walk.
(Use a float number between but not equal to 0 and 1.)

Following code shows an example. The RAWR random walk will conduct with reverse rate equals to 0.3.

```python
>>> rawr = RAWR.sampler.rawrSampler(alnFile, outputDir, reverseRate=0.3)
>>> rawr.sampleSeqs()
```

#### **SERES resampler**

For SERES sequence resampling, use *seresSampler* instead of *rawrSampler*.
User can also assign anchor parameters for the *seresSampler* instance. 
The *anchornum* controls the number of anchors. and the *anchorLen* is the length of each anchor.  
```python
>>> seres1 = RAWR.sampler.seresSampler(alnFile, outputDir)
>>> seres1.sampleSeqs()
>>> seres2 = RAWR.sampler.seresSampler(alnFile, outputDir, anchorNum=10, anchorLen=15)
>>> seres2.sampleSeqs()
```

### **Estimate support**
After sequence resampling, you can calculate MSA support or phylogenetic tree support by *RAWR.supportEstimator*. 

The phylogenetic tree support estimation requires an input tree file. 
Download this [alignment file](exampleData/alignment.fasta) and [phylogenetic tree file](exampleData/infer.tree) to your device.

Copy the downloaded files to the directory that you would like to save the analysis results and sampled sequences. 
```bash
cp $downloadDir/alignment.fasta $workDir/
cp $downloadDir/infer.tree $workDir/
```
Then start a interactive Python environment or open a Python script.

#### **MSA support estimation**
You can estimate the support of a MSA by sequence resampling. 
The MSA in the input alignment file will be the annotated MSA. 

First generate sample sequences by *rawrSampler* or *seresResampler*.
Then create an instance of *msaSupportEstimator*. 
Use *msaSupportEstimator.calculateSupport()* to calculate support value for input alignment. 

Following code samples 100 sequences from input alignment by RAWR with default reverse rate. 
Then re-estimate alignments for sampled sequences by MAFFT.
Please make sure that you have installed MAFFT correctly by type "mafft -h". 
You can download MAFFT [here](https://mafft.cbrc.jp/alignment/software/).
Re-estimated alignments will be saved to *outputDir/samples/*.
Then the MSA support will be calculated based on re-estimated alignments. 
The MSA support value will be saved to *outputDir/MSA.support.csv*. 

More details about the output file shows in [Output](#output) chapter.


```python
>>> import RAWR
>>> alnFile = "ABSOLUTE_PATH_OF_WORKDIR/alignment.fasta"
>>> outputDir = "ABSOLUTE_PATH_OF_WORKDIR/"
>>> rawr = RAWR.sampler.rawrSampler(alnFile, outputDir)
>>> rawr.sampleSeqs()
>>> msa = RAWR.supportEstimator. msaSupportEstimator(alnFile, outputDir,
...             "mafft", samplenum=100)
>>> msa.calculateSupport()
```

#### **Tree support estimation**
You can estimate the support of a phylogenetic tree by sequence resampling. 
You need to input an alignment file and a tree file. 
The input alignment are required to share the same taxa set of the input tree. 

First use *rawrSampler* or *seresResampler* to generate sample sequences.
Then create an instance of *treeSupportEstimator*. 
Use *treeSupportEstimator.calculateSupport()* to calculate support value for input alignment. 

Following code samples 10 sequences from input alignment by RAWR with default reverse rate. 
Then re-estimate alignments for sampled sequences by MAFFT.
Please make sure that you have installed MAFFT correctly by type "mafft -h". 
You can download MAFFT [here](https://mafft.cbrc.jp/alignment/software/).
The 
Re-estimated alignments will be saved to *outputDir/samples/*.
Then sampled tree is inferred for each re-estimated alignment by RAxML. 
Please make sure that you have installed RAxML correctly by type "raxmlHPC -h". 
You can download RAxML [here](https://github.com/stamatak/standard-RAxML).
Then the tree support will be calculated based on sampled trees. 

The tree support value will be saved to *outputDir/tree.support.txt*.
An user-friendly figure for the annotated tree is also created and saved to *tree.support.png*. 

More details about the output file shows in [Output](#output) chapter.


```python
>>> import RAWR
>>> alnFile = "ABSOLUTE_PATH_OF_WORKDIR/alignment.fasta"
>>> outputDir = "ABSOLUTE_PATH_OF_WORKDIR/"
>>> inputTreeFile = "ABSOLUTE_PATH_OF_WORKDIR/infer.tree"
>>> rawr = RAWR.sampler.rawrSampler(alnFile, outputDir)
>>> rawr.sampleSeqs()
>>> tree = RAWR.supportEstimator. treeSupportEstimator(alnFile, outputDir, 
...             "mafft", "raxmlHPC", 
...             inputTreeFile)
>>> tree.calculateSupport()
```

### **Running on large dataset**
The sequence resampling process can be very fast on vary large dataset. 
However, for support estimation, the bottle neck is re-estimation of alignments and trees for each sampled sequence. 

Therefore, for support estimation of very large dataset, we recommend you only use *RAWR.sampler* to generate sequences. Then manually re-estimate alignments and trees for sampled sequences. You can apply any parallel computing pipeline as you like to improve the execute time. 

Following code shows the alignment and tree re-estimation step for each sample sequence. 
```bash
cd ${workDir}/samples/
mafft ${i}.seq.fasta > ${i}.aln.fasta
raxmlHPC -f a -s ${i}.aln.fasta -n ${i} -m GTRGAMMA -p $RANDOM -x $RANDOM -w ${workDir} -N 10
```

Following code shows the tree support estimation using sequential resampled trees by RAxML.
```bash
cd ${workDir}/
cat samples/RAxML_bestTree.* > sampled.trees
raxmlHPC -f b -m GTRGAMMA -t infer.tree -z sampled.trees -n tree.support
```

## **Output**
### **sample sequence output**
The sequence sampler will create a directory *samples/* under the work directory. 
The sampled sequences and indics will be saved to *samples* under the work directory. 

An example of the *samples/* directory shows as following. 

![sampleDir](figure/sampledSequences.png)

**.seq.fasta*: the unaligned sampled sequence in FASTA format.

**.index*: this file contains a series of column indices of the input alignment. The column indices represent sampled columns of input alignment in order. 

### **MSA support estimation output**
The MSA support estimator will calculate the support value for input alignment and save the support value to *MSA.support.csv* located at the work directory.

*MSA.support.csv* contains four columns. 
The first column locates the position of a residue pair. 
The first column is the column index of the residue pair. 
The second column and third column are the row index of two residues in this residue pair. 
Only residue pairs with no gaps are contained in this result.  
The last column is the support value, which ranges from 0 to 1. 
![MSA support](figures/msaSupport.png)


### **Tree support estimation output**
The tree support estimator will generate annotated tree in Newick format, which is saved to *tree.support.txt* in the work directory. It will also generate a figure shows the tree structure and support value.
The figure will be saved to *tree.support.png*.

Annotated tree shows as following. You can use this annotated tree for other analysis. 
You can also plot your own figure using this annotated tree by online tool [evolview](https://evolgenius.info/evolview-v2/).
![annotated tree](treeSupportTxt.png)
The tree figure shows as following. On each internal branch, the black number is the branch length. The red number is the support value ranges from 0 to 100. 
![tree figure](treeSupportPng.png)

<!-- ## **Miscellaneous** -->
