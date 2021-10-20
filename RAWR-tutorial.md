RAndom Walk Resampling (RAWR) is a non-parametric resampling method written in Python. We provide two resampling algorithms here, RAWR and SEquential
RESampling (SERES), for performing support estimation for multiple sequence alignments and phylogenetic trees. RAWR and SERES conceptually work by random walking along biological molecular sequences and retaining the sequence dependence during the resampling process. We present this tutorial for people who prefer to use a GUI application or a website interface. The algorithms are freely available under the GNU General Public License.

* [Installation](#installation)
  * [Software Install](#software-install)
  * [Website Install](#website-install)
* [Input](#input)
* [Output](#output)
  * [sample sequence output](#sample-sequence-output)
  * [MSA support estimation output](#msa-support-estimation-output)
  * [Tree support estimation output](#tree-support-estimation-output)
* [Publications](#publications)

Installation
---------------

### Software Install

RAWR has a GUI interface tested to work on Linux and Mac. Before running RAWR, please make sure you have set `python` to link to Python3. We have last checked on Python 3.8 that the following python dependencies' versions are compatible:
```
 pip install ete3==3.1.2
 pip install PyQt5==5.11.3
 pip install 'numpy>=1.14.6'
 pip install 'scipy>=1.1.0'
 pip install sklearn
 pip install 'Bio>=1.1.5'
 pip install 'flask>=2.0.2'
 pip install 'pytz>=2021.3'
 pip install 'python-dateutil>=2.7.3'
```
To run RAWR GUI software, launch open a command `terminal` in the RAWR-software directory and type in `python main.py`. 
Our RAWR software comes with standalone versions of MAFFT version 7.487 and RAxML version 8.2.12. If you want updated versions of these software, you can download the standalone versions from their respective websites and replace their folders in `src`.
  - MAFFT: https://mafft.cbrc.jp/alignment/software/.
  - RAxML: https://github.com/stamatak/standard-RAxML.

### Website Install

We provide the codebase to set up a RAWR web server to run on your local host. The users can directly use the web server for small dataset analysis. The limitation of the web server is 50Mb of the input alignment with no more than 50 taxa.
First, please make sure you have set `python` to link to Python3 and make sure your python version is dependencies' versions are compatible:
```
 pip install ete3==3.1.2
 pip install PyQt5==5.11.3
 pip install 'numpy>=1.14.6'
 pip install 'scipy>=1.1.0'
 pip install sklearn
 pip install 'Bio>=1.1.5'
 pip install 'flask>=2.0.2'
 pip install 'pytz>=2021.3'
 pip install 'python-dateutil>=2.7.3'
```
Second, set up your server e-mail. Its purpose is to notify the user when and where the results can be found, in case they time out or exit from the homepage, they will still get the result safely delivered via email when it's ready. We have included below instructions to set up any Gmail account as a server sender. Setting up Flask to use an existing emailing service is the easiest way to use the asynchronous emailing option, but feel free to adapt the code to your needs.

Step 1: Turn on "Less secure app access" for your specific gmail account here: https://myaccount.google.com/lesssecureapps 

Step 2: Edit the app.py in the following app.config.update() section. 
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
1) If you have NOT enabled 2-step verification, enter your gmail login password
2) If you have enabled 2-step verification, generate and use an app password


To run RAWR web server, open a command `terminal` in the RAWR-web directory and type in `python app.py`. The local host that Python's Flask package will probably choose is `http://10.0.2.15:5000/`, and you can visit this locally hosted page with any browser. You can double check the link by looking for the terminal printed statement `* Running on http://10.0.2.15:5000/ (Press CTRL+C to quit)`.

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
Publications
---------------
Wang, W., Hejasebazzi, A., Zheng, J., & Liu, K. J. (2021). Build a better bootstrap and the RAWR shall beat a random path to your door: phylogenetic support estimation revisited. Bioinformatics, 37(Supplement_1), i111-i119.

Wang, W., Smith, J., Hejase, H. A., & Liu, K. J. (2020). Non-parametric and semi-parametric support estimation using SEquential RESampling random walks on biomolecular sequences. Algorithms for Molecular Biology, 15(1), 1-15.

Wang, W., Wuyun, Q., & Liu, K. J. (2019, November). An application of random walk resampling to phylogenetic HMM inference and learning. In 2019 IEEE International Conference on Bioinformatics and Biomedicine (BIBM) (pp. 44-51). IEEE.
