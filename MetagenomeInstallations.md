## From microbial genomes to metagenomes  
*Antti Karkman, 2017* 

# Installations
Log into Taito, either with ssh (Mac/Linux) or PuTTy (Windows)  

**Anaconda2** (takes ~5 min)  
Go to https://www.anaconda.com/download/#linux with your local browser and copy the link address of the Python 2.7 64-bit (x86) Installer
Or just trust me and use this: https://repo.continuum.io/archive/Anaconda2-5.0.1-Linux-x86_64.sh  

In Taito go to your application folder and download the installations script.
```
cd $USERAPPL
# use wget to download
# And then run
bash Anaconda2-5.0.1-Linux-x86_64.sh  
```

**MultiQC**  
For combining multiple QC reports.  
`conda install -c bioconda multiqc`

**_STOP HERE_**

**Anvi'o**  
Create a virtual environemnt for Anvi'o and install all dependencies using anaconda (takes 5–10 min)  
`conda create -n anvio3 -c bioconda -c conda-forge python=3.5.4 gsl anvio`  

Let's test it  
```
source activate anvio3
anvi-self-test --suite mini
```
We will also need to install NCBIs COG databases and reformat them so they can be used later. The formatting step includes changing reorganizing information in raw files, serializing a very large text file into binary Python object for fast access while converting protein IDs to COGs, and finally generating BLAST and DIAMOND search databases.

```
anvi-setup-ncbi-cogs --num-threads 20
```
All should be good and we can close the virtual environment.  
`source deactivate`  

**Centrifuge**  
For taxonomic annotation of contigs in Anvi'o.
```
cd $USERAPPL
git clone https://github.com/infphilo/centrifuge
cd centrifuge
# We need a certain version, so checkout the branch specified
git checkout 30e3f06ec35bc83e430b49a052f551a1e3edef42
make
# Test it, should be version 1.0.2-beta  
`./centrifuge --version`  
```
Download the pre-computed indexes for centrifuge (Can take 10 min).  
Since they are very big, it's better to put them in the `$WRKDIR`, since the home directory is quite small and not meant for storage for large files.  
```
cd $WRKDIR
mkdir centrifuge_db
cd centrifuge_db
wget ftp://ftp.ccb.jhu.edu/pub/infphilo/centrifuge/data/p+h+v.tar.gz
tar -zxvf p+h+v.tar.gz && rm -rf p+h+v.tar.gz
```

Set an environmental variable to the centrifuge folder.
You need to change the path to _your_ centrifuge folder.
```
export CENTRIFUGE_BASE="/wrk/antkark/centrifuge_db"
echo $CENTRIFUGE_BASE
# Needs to be done every time after logging out
```

OR you can add it to your `.bashrc`.  
Go to home folder and open `.bashrc` with a text editor.  
Add things after the `# User specific aliases and functions`. Make sure they pointy to the right place on your own folders.  
```
export CENTRIFUGE_BASE="$WRKDIR/centrifuge_db"
# You can add also the centrifuge executable to your PATH
export PATH=$PATH:$USERAPPL/centrifuge
```
If you did set the env variable before, you can remove it first and then set it thru `.bashrc`.  
```
unset CENTRIFUGE_BASE
echo $CENTRIFUGE_BASE # This should give an empty row at this point

#Then run
source .bashrc
# And test that it worked.
centrifuge --version
echo $CENTRIFUGE_BASE
```

**Metaxa2**  
For taxonomic profiling of the samples using the rimmed reads.
```
cd $USERAPPL
wget http://microbiology.se/sw/Metaxa2_2.1.3.tar.gz
tar -xzvf Metaxa2_2.1.3.tar.gz
cd Metaxa2_2.1.3
```
Test it, you will need to load the biokit first, because Metaxa2 uses HMMER3 and BLAST.
```
module load biokit
./metaxa2 -i test.fasta -o TEMP --plus
```
Then you can remove the results  
`rm TEMP*`  

You can also add Metaxa2 to your PATH (go to --> `.bashrc`)  

**VSEARCH**  
For functional gene annotations, in our case ARGs.
```
cd $USERAPPL
wget https://github.com/torognes/vsearch/releases/download/v2.6.0/vsearch-2.6.0-linux-x86_64.tar.gz
tar -xzvf vsearch-2.6.0-linux-x86_64.tar.gz
```
VSEARCH can be found from the `bin` folder. You can add that to your PATH as well.  
