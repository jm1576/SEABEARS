# SEABEARS


## BACKGROUND
For this project, we are comparing the genome assembly of _Hypsibius dujardini_ (tardigrade, aka sea bears) between two different assembling programs through conda. We are following a previous study which used SPAdes as the de novo genome assembler. The assembler we will be comparing to SPAdes is Velvet. Both SPAdes and Velvet are de novo genome assemblers most often used for small genomes and takes paired-ends reads in FASTQ formatting. Velvet was inititally released in 2008, while SPAdes is much newer, having been fully released in 2021. 

### ORGANISM BACKGROUND
Tardigrades (a.k.a. water bears or moss piglets)
- Invertebrates belonging to the phylum Tardigrada
- Close relative to arthropods
- Large number of genera and species
- Variety of habitats: damp moss, sand, fresh or salt water


![alt text](https://i.natgeofe.com/n/7d80b867-3977-4f36-8d33-b64ad03431d9/01-tardigrades-sciencesource_ss2437867.jpg)
![alt text](https://static.wikia.nocookie.net/non-aliencreatures/images/1/12/SeaBear.png/revision/latest?cb=20140923150713)


## METHODS
### General Pipeline
First, we will be using two separate _De novo_ genome assemblers called Canu and SPAdes. We are inputting the same Tardigrade forward and reverse reads into each system. Once we have both genome assemblies, we will be using the genome assembly evaluation tool (QUAST) to determine the quality of each assembly from the two separate pipelines.  

```mermaid
graph TD;
    id1(Tardigrade FASTQs from Illumina) --> id2(SPAdes genome assembler);
    id1(Tardigrade FASTQs from Illumina) --> id3(Velvet genome assembler);
    id2(SPAdes genome assembler) --> id4(Genome Assembly Evaluation Tool QUAST);
    id3(Velvet genome assembler) --> id4(Genome Assembly Evaluation Tool QUAST);
    
```
### Commands we used

#### SPAdes
```
## Downloading SPAdes de novo genome assembler
conda install -c bioconda spades

## Downloading reads for assembly programs
curl -L -o forwardreads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_1.fastq.bz2
curl -L -o reversereads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_2.fastq.bz2

## Converting bz2 to gz for assembly program use
bzcat forwardreads.fastq.bz2 | gzip -c >forwardreads.fastq.gz
bzcat reversereads.fastq.bz2 | gzip -c >reversereads.fastq.gz

## Trim reads for program
trimmomatic PE forwardreads.fastq.gz reversereads.fastq.gz \
    forwardreads.trimmed.fastq.gz forwardreadsun.trimmed.fastq.gz \
    reversereads.trimmed.fastq.gz reversereadsun.trimmed.fastq.gz \
    SLIDINGWINDOW:4:20 -phred33
    
## Running SPAdes program on reads
spades.py -1 forwardreads.trimmed.fastq.gz -2 reversereads.trimmed.fastq.gz -o SPAdesoutput --isolate

```
#### Velvet
```
## Downloading Velvet de novo genome assembler


## Downloading reads for assembly programs
curl -L -o forwardreads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_1.fastq.bz2
curl -L -o reversereads.fastq.bz2 https://ddbj.nig.ac.jp/public/ddbj_database/dra/fastq/DRA004/DRA004455/DRX049724/DRR055040_2.fastq.bz2

## Converting bz2 to gz for assembly program use
bzcat forwardreads.fastq.bz2 | gzip -c >forwardreads.fastq.gz
bzcat reversereads.fastq.bz2 | gzip -c >reversereads.fastq.gz

## Trim reads for program
trimmomatic PE forwardreads.fastq.gz reversereads.fastq.gz \
    forwardreads.trimmed.fastq.gz forwardreadsun.trimmed.fastq.gz \
    reversereads.trimmed.fastq.gz reversereadsun.trimmed.fastq.gz \
    SLIDINGWINDOW:4:20 -phred33
    
## Running Velvet program on reads

```

### QUAST
```
## Installing environment for QUAST
conda create -n quast-env python=3.6
conda activate quast-env

## Running QUAST on SPAdes output
quast.py -o quastoutput contigs.fasta

## Running QUAST on Velvet output

```

## RESULTS

![Screen Shot 2022-05-16 at 12 15 05 PM](https://user-images.githubusercontent.com/103778088/168637962-8166970a-97db-44b4-8035-37359151517f.png)
![Screen Shot 2022-05-16 at 12 15 30 PM](https://user-images.githubusercontent.com/103778088/168637964-932091fe-4b93-4b39-a1aa-684dd4d7a98d.png)
![Screen Shot 2022-05-16 at 12 15 37 PM](https://user-images.githubusercontent.com/103778088/168637966-8f12e969-602e-423d-991b-f4de1acef980.png)
![Screen Shot 2022-05-16 at 12 15 43 PM](https://user-images.githubusercontent.com/103778088/168637967-a38b535a-1d6b-4ebc-8102-ae113146a07f.png)
![Screen Shot 2022-05-16 at 12 15 53 PM](https://user-images.githubusercontent.com/103778088/168637968-8863790a-28d0-463d-859d-adb3f4085cd4.png)
![Screen Shot 2022-05-16 at 12 16 00 PM](https://user-images.githubusercontent.com/103778088/168637970-c67ca16f-1090-4c96-ba91-56667be51e69.png)

## CONCLUSION
### SPAdes
SPAdes is not allowing two fastq files with different lengths to be run together on the program. The next ideas will be:

- Trimming the forward read (which is longer)
- Finding a way to override this error
- Assembling the forward and reverse reads individually

### Velvet
Using the forward read alone in Canu yielded no results. Trouble shooting options:

- Retrying forward read with different combinations of options/different command
- Downloading a new program entirely--some options we have looked into are PANDAseq and Velvet (Velvet seems more promising)


downloading files: 

https://en.wikipedia.org/wiki/De_novo_sequence_assemblers
https://canu.readthedocs.io/en/latest/quick-start.html
https://canu.readthedocs.io/en/latest/tutorial.html#tutorial
https://anaconda.org/bioconda/canu
