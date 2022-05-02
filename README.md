# SEABEARS


## BACKGROUND
For this project, we are comparing the genome assembly of _Hypsibius dujardini_ (tardigrade, aka sea bears) between two different assembling programs through conda. We are following a previous study which used SPAdes as the de novo genome assembler. The assembler we will be comparing to SPAdes is Canu. SPAdes is a de novo genome assembler most often used for small genomes and takes paired-ends reads in FASTQ formatting. Canu is generally used for assembling PacBio/Nanopore short reads, but can sometimes be used for assembling long reads. We originally found the programs before finding the reads ( it was extremely troubling to find usable reads), so we were going to try using these programs before trying new ones. 

### ORGANISM BACKGROUND
Tardigrades (a.k.a. water bears or moss piglets)


Invertebrates belonging to the phylum Tardigrada


Close relative to arthropods


Large number of genera and species


Variety of habitats: damp moss, sand, fresh or salt water


![alt text](https://i.natgeofe.com/n/7d80b867-3977-4f36-8d33-b64ad03431d9/01-tardigrades-sciencesource_ss2437867.jpg)
![alt text](https://static.wikia.nocookie.net/non-aliencreatures/images/1/12/SeaBear.png/revision/latest?cb=20140923150713)


## METHODS

### COMMANDS WE USED 

##### Installing assembly programs
###### Canu de novo genome assembler
`conda install -c bioconda canu`
###### SPAdes de novo genome assembler
`conda install -c bioconda spades`

##### Downloading reads for assembly programs
###### Forward reads
`curl -L -o forwardreads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_1.fastq.bz2>`
###### Reverse reads
`curl -L -o reversereads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_2.fastq.bz2`

##### Converting bz2 to gz for assembly program use
###### Forward reads
`bzcat forwardreads.fastq.bz2 | gzip -c >forwardreads.fastq.gz`
###### Reverse reads
`bzcat reversereads.fastq.bz2 | gzip -c >reversereads.fastq.gz`

##### Running Spades program on reads
`spades.py -1 forwardreads.fastq.gz -2 reversereads.fastq.gz -o SPAdespacbio.fastq.gz --isolate`

##### Running Canu program on reads
`canu -p canu -d canu_assembly genomeSize=245m -pacbio-raw forwardreads.fastq.gz`


## Troubleshooting
### SPAdes
SPAdes is not allowing two fastq files with different lengths to be run together on the program. The next ideas will be:

- Trimming the forward read (which is longer)
- Finding a way to override this error
- Assembling the forward and reverse reads individually

### Canu
Using the forward read alone in Canu was not possible. Trouble shooting options:

- retrying forward read with different combinations of options/different command
- Downloading a new program entirely--some options we have looked into are PANDAseq and Velvet (Velvet seems more promising)


downloading files: 


https://en.wikipedia.org/wiki/De_novo_sequence_assemblers
https://canu.readthedocs.io/en/latest/quick-start.html
https://canu.readthedocs.io/en/latest/tutorial.html#tutorial
https://anaconda.org/bioconda/canu
