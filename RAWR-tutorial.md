RAWR is a non-parametric resampling method written in Python. We provide two resampling algorithms here, RAndom Walk Resampling (RAWR) and SEquential
RESampling (SERES), for performing support estimation for multiple sequence alignments and phylogenetic trees. RAWR and SERES conceptually work by random walking along biological molecular sequences and retaining the sequence dependence during the resampling process. We present this tutorial for people who prefer to use a GUI application or a website interface. Picture tutorials are available in the supplementary document of the associated publication. The algorithms implemented in RAWR are freely available under the GNU General Public License. 

* [Installation](#installation)
  * [Software Install](#software-install)
  * [Website Install](#website-install)
  * [Local Galaxy Install](#local-galaxy-install)
* [Input](#input)
* [Output](#output)
  * [sample sequence output](#sample-sequence-output)
  * [MSA support estimation output](#msa-support-estimation-output)
  * [Tree support estimation output](#tree-support-estimation-output)
* [Visualize Output](#visualize-output)
  * [MSA support visualizer](#msa-support-visualizer)
  * [Tree support visualizer](#tree-support-visualizer)
  * [Galaxy tree support visualizer](#galaxy-tree-support-visualizer)
* [Publications](#publications)

Installation
---------------

### Software Install

RAWR has a GUI interface tested to work on Apple macOS Big Sur, Linux Ubuntu 20.04, and Windows. Before running RAWR, please make sure to install and make default Python3. You may use Anaconda or Virtualenv to manage python packages. We have last checked on Python 3.8 that the following python dependencies are compatible.
```
#Virtualenv commands
pip install ete3==3.1.2
pip install PyQt5==5.11.3
pip install "numpy>=1.14.6"
pip install "scipy>=1.1.0"
pip install sklearn
pip install "Bio>=1.1.5"
pip install "flask>=2.0.2"
pip install pathlib
pip install "pytz>=2021.3"
pip install "python-dateutil>=2.7.3"
pip install pandas
```
If you prefer Anaconda package manager, use the command below to install dependencies.
```
#Anaconda commands
conda install -c conda-forge -c etetoolkit ete3 pyqt numpy scipy scikit-learn biopython flask pathlib pytz python-dateutil pandas
```
To run RAWR GUI software, launch open your computer's command terminal in your RAWR-software directory and enter the command, `python main.py`. 
Our RAWR software comes with standalone versions of MAFFT version 7.487 and RAxML versions 8.2.10 (for macOS and Windows) and version 8.2.12 for Linux. If you want updated versions of these software, you can download the standalone versions from their respective websites and replace their binaries in `src` (please make sure to double check that the binary filenames remain the same).
  - MAFFT: https://mafft.cbrc.jp/alignment/software/.
  - RAxML: https://github.com/stamatak/standard-RAxML.

### Website Install

We provide the codebase to set up a RAWR web server to run on your local host. Users can directly use the web server for small dataset analysis. The limitation of the web server is 50Mb of the input alignment with no more than 50 taxa.

First, please make sure your local host's operating system is one of the following: Linux, Apple OSX, or Windows 10. If you are using Windows, please install Windows Subsystem for Linux and reboot prior to setting up the RAWR web service. If you are using macOS, please change your terminal shell to Bash.

Please make sure you have set `python` to link to Python3 and make sure to install the following python dependencies:
```
#Virtualenv commands
pip install ete3==3.1.2
pip install PyQt5==5.11.3
pip install "numpy>=1.14.6"
pip install "scipy>=1.1.0"
pip install sklearn
pip install "Bio>=1.1.5"
pip install "flask>=2.0.2"
pip install pathlib
pip install "pytz>=2021.3"
pip install "python-dateutil>=2.7.3"
pip install pandas
```
If you prefer Anaconda package manager, use the command below to install dependencies.
```
#Anaconda commands
conda install -c conda-forge -c etetoolkit ete3 pyqt numpy scipy scikit-learn biopython flask pathlib pytz python-dateutil pandas
```
Second, set up your server e-mail. Its purpose is to notify the user when and where the results can be found, in case they time out or exit from the homepage, they will still get the result safely delivered via email when it's ready. We have included below instructions to set up any Gmail account as a server sender. Setting up Flask to use an existing emailing service is the easiest way to use the asynchronous emailing option, but feel free to adapt the code to your needs.

Step 1: Turn on "Less secure app access" for your specific gmail account here: https://myaccount.google.com/lesssecureapps 

Step 2: Edit the file `app.py` in the following app.config.update() section. 
```
 app.config.update(
    MAIL_SERVER='smtp.gmail.com',
    MAIL_PORT=465,          
    MAIL_USE_SSL=True,      
    MAIL_USERNAME='xxx@gmail.com',
    MAIL_PASSWORD='xxx'
)
```
When editing the MAIL_PORT, please choose from the two options below:
1) If using STARTTLS with MAIL_USE_TLS = True, then use MAIL_PORT = 587.
2) If using SSL/TLS directly with MAIL_USE_SSL = True, then use MAIL_PORT = 465.


Step 3: Enter your MAIL_USERNAME as your full gmail account and MAIL_PASSWORD as one of the following 2 options: 
1) If you have NOT enabled 2-step verification, enter your gmail login password for MAIL_PASSWORD
2) If you have enabled 2-step verification, generate and use an app password for MAIL_PASSWORD


To run RAWR web server, open a command `terminal` in the RAWR-web directory and type in `python app.py`. The local host that Python's Flask package will probably choose by default is `http://10.0.2.15:5000/`. Users can visit this locally hosted page with any browser. While launching the server, you can double check the website link by looking for the terminal printed statement `* Running on http://10.0.2.15:5000/ (Press CTRL+C to quit)`.

### Local Galaxy Install
We recommend Anaconda for python package management, but you can choose Virtualenv.

First, follow instructions to install local Galaxy server. https://galaxyproject.org/admin/get-galaxy/

Second, follow instructions to install Planemo. https://anaconda.org/bioconda/planemo 

Third, get your RAWR executable. If you are using Linux, the executable is available. https://gitlab.msu.edu/liulab/rawr-web-software .
If you are using other operating systems, please download the `rawr-for-galaxy` folder from the above gitlab link and create an executable on your machine:
```
pyinstaller --hidden-import="sklearn.utils._cython_blas" --hidden-import="sklearn.neighbors.typedefs" rawrsampler.py
```
Then, copy `mafft` and `raxml` folders from `rawrsampler` folder to the binary `build/rawrsampler` folder.

Fourth, copy the provided `conda-recipe` folder and `rawrsampler.xml` file to the `build` folder. Open a command terminal in the `build` folder path and build the conda package locally.
```
#After activating your Anaconda environment that you installed Planemo into:
conda build conda-recipe
```
Finally, use Planemo to serve the rawrsampler tool on your local Galaxy instance. 
```
planemo serve --conda_use_local --galaxy_root ~/Work/galaxy
```
By default, Planemo will likely host the local Galaxy instance at `http://127.0.0.1:9090/`. Users can visit this locally hosted page with any browser.
---------------
Input
---------------
If you are running RAWR for MSA estimation, please prepare a multi-species alignment file in FASTA format. 
If you are running RAWR for phylogeny alignment estimation, please prepare a multi-species alignment file and a phylogeny in Newick tree format.

In this tutorial, we will not go over preparing the inputs. Instead, we provide two small datasets to try out RAWR. These two datasets have been prepared from sequence files and aligned with MAFFT. Then, we used the alignment file as input into RAxML and reconstructed a phylogeny under the General Time Reversal (GTR) model. 

1) Launch a command `terminal` in your RAWR directory. For the software RAWR, enter `python main.py`. For the web server RAWR, enter comand `python app.py` and open a browser to `http://10.0.2.15:5000/` or whichever localhost your server is using (if you need help, see installation for web server for more details). 

2) Find the example datasets in your RAWR directory under the `examples` folder. Here, you should see `dataset1-5taxa` and `dataset2-10taxa`. We recommend starting with the smaller dataset,`dataset1-5taxa`, and returning to the larger dataset, `dataset2-10taxa`, later.

3) Select either `RAWR` or `SERES` as resampling algorithm.

4) Select either `multiple sequence alignment (MSA)` estimation or `phylogenetic tree` estimation.

5) Optionally, you can edit the default parameter values.
```
By default:
Sample Number: 10 (minimum 2. Represents total number of sampled sequences.)
Reverse Rate: 0.1 (0 to 1 not including 0 or 1. Represents the reverse rate of the random walk.)
Anchor Number: 20 (SERES only. Represents the number of anchors.)
Anchor Length: 5 (SERES only. Represents the length of the anchors.)
```

6) Select a file input for the FASTA alignment file. In our example datasets, choose `alignment.fasta`

7) Copy-paste the contents of `infer.tree` into the phylogeny tree textbox.

8) If you are using the website version of RAWR, you can optionally enter a user email address to send the results.

9) Click `OK` or `Submit` and let the program run to completion. Please note that if you are using the software GUI version of RAWR, it is best to provide an empty results folder to avoid any problems with RAxML refusing to run due to previous runs' files present or improper software termination.


---------------
Output
---------------

The Sequence sampler will create a tar compressed directory called `samples`. The sampled sequences and indices can be found here.

```
*.seq.fasta: the unaligned sampled sequence in FASTA format.

*.index: this file contains a series of column indices of the input alignment. The column indices represent sampled columns of input alignment in order.
```

### MSA support estimation output

The MSA support estimator will calculate the support value for input alignment and save the support value to `MSA.support.csv`. 

`MSA.support.csv` contains four columns. The first column locates the position of a residue pair. The second column and third column are the row index of two residues in this residue pair. Only residue pairs with no gaps are contained in this result. The last column is the support value, which ranges from 0 to 1.

### Tree support estimation output

The tree support estimator will generate annotated tree in Newick format, which is saved to `tree.support.txt`. It will also generate a figure called `tree.support.png` to visualize the tree structure and support values. 

---------------
Visualize Output
---------------

### MSA support visualizer
We provide a python script called `MSA_support_csv_2_jalview_sequence_annotation.py` that will take the output file `MSA.support.csv` and generate a JalView Sequence Features annotation file. This script is accessible by command line `python MSA_support_csv_2_jalview_sequence_annotation.py MSA.support.csv`. The support bar graph color can be changed with our custom script `python MSA_support_csv_2_jalview_sequence_annotation.py -color blue MSA.support.csv`. 

We used JalView 2.11.1.4 in the following example.
Click “File” > “Input Alignment” > “From File” to enter your original “alignment.fasta” input file. This will open an alignment viewer inside of JalView. Inside the alignment viewer, click “File” > “Load Features/ Annotations”. 
 
### Tree support visualizer
We provide a visualization of the Newick tree with confidence support. You can also choose other phylogeny visualizers such as Interactive Tree Of Life (iTOL). 

### Galaxy tree support visualizer
Galaxy interface has visualization software PhyloViz that can be accessed via the visualization icon below the output phylogeny tree.

---------------
Publications
---------------
Wang, W., Hejasebazzi, A., Zheng, J., & Liu, K. J. (2021). Build a better bootstrap and the RAWR shall beat a random path to your door: phylogenetic support estimation revisited. Bioinformatics, 37(Supplement_1), i111-i119.

Wang, W., Smith, J., Hejase, H. A., & Liu, K. J. (2020). Non-parametric and semi-parametric support estimation using SEquential RESampling random walks on biomolecular sequences. Algorithms for Molecular Biology, 15(1), 1-15.

Wang, W., Wuyun, Q., & Liu, K. J. (2019, November). An application of random walk resampling to phylogenetic HMM inference and learning. In 2019 IEEE International Conference on Bioinformatics and Biomedicine (BIBM) (pp. 44-51). IEEE.
