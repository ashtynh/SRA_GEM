# SRA2GEM

##Lab Overview
In this lab you will learn how to pull SRA files using the SRA-toolkit. Then you will learn how to construct a GEM (gene-expression matrix). To do this, we will utilize GEMMaker, a nextflow workflow that runs all the steps needed to turn a set of fastq files to a GEM. Lastly, we will visualize the GEM as a histogram to analyze the levels of expression in the files of interest. 

##Learning Objectives
1. *Identify human SRA files from the SRA archive*
2. *Create a GEM*
3. *Normalize the GEM*
4. *Analyze the GEM*

##Lab Tasks
*Step 1: Download the sra-toolkit*
wget http://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/current/sratoolkit.current-ubuntu64.tar.gz

*Step 2: Install sra-toolkit and configure*
tar -vxzf sratoolkit.current-ubuntu64
vdb-config -i

*Step 3: Download SRA file in .sra format*
prefetch SRA_FILE

*Step 4: Convert to human-readable FASTQ*
fasterq-dump SRA_FILE

*Step 5: Install Nextflow*
wget -qO- https://get.nextflow.io | bash
    make sure the nextflow binary is in your PATH
    
*Step 6: Install GO*
wget -O /tmp/go${VERSION}.${OS}-${ARCH}.tar.gz \
https://dl.google.com/go/go${VERSION}.${OS}-${ARCH}.tar.gz
sudo tar -C /usr/local -xzf /tmp/go${VERSION}.${OS}-${ARCH}.tar.gz

*Step 7: Install singularity*
git clone https://github.com/sylabs/singularity.git
cd singularity
./mconfig
make -C builddir
sudo make -C builddir install

*Step 8: Make sure all necessary binaries are in your PATH*

*Step 9: Download and index reference fasta file for GEM*
wget /path/to/file
singularity exec -B ${PWD} https://depot.galaxyproject.org/singularity/kallisto:0.46.2--h4f7b962_1 kallisto index -i FILENAME.indexed FILENAME

*Step 10: Pick experiment identifiers from NCBI SRA*
nano SRAs.txt
paste experiment identifiers

*Step 11: Build the GEM*
nextflow run systemsgenetics/gemmaker -profile singularity \
--pipeline kallisto \
--kallisto_index_path FILE.indexed \
--sras SRAs.txt

*Step 12: Create gemprep conda enviroment*
conda create -n gemprep python=3.6 matplotlib mpi4py numpy pandas r scikit-learn seaborn
source activate gemprep

*Step 13: Clone GEMprep*
git clone https://github.com/SystemsGenetics/GEMprep
cd GEMprep

*Step 14: Quantile normalize GEM*
python ./GEMprep/bin/normalize.py GEM.txt GEM.quantile.txt --quantile

*Step 15: Log 2 transformation*
python ./GEMprep/bin/normalize.py GEM.quantile.txt GEM.quantile.log2.txt --log2

*Step 16: Analyze GEM by producing a histogram*
python ./GEMprep/bin/visualize.py GEM.quantile.log2.txt --density HIST-GEM.png


