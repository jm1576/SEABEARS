# SEABEARS


## BACKGROUND
For this project, we are comparing the genome assembly of _Hypsibius dujardini_ (tardigrade, aka sea bears) between two different assembling programs through conda. We are following a previous study (SOURCE) which used SPAdes as the de novo genome assembler. The assembler we will be comparing to SPAdes is Canu. SPAdes is a de novo genome assembler most often used for small genomes and takes paired-ends reads in FASTQ formatting.

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
## FINDINGS


downloading files: 


https://en.wikipedia.org/wiki/De_novo_sequence_assemblers
https://canu.readthedocs.io/en/latest/quick-start.html
https://canu.readthedocs.io/en/latest/tutorial.html#tutorial
https://anaconda.org/bioconda/canu
