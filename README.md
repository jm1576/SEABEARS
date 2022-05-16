# SEABEARS


## BACKGROUND
For this project, we are comparing the genome assembly of _Hypsibius dujardini_ (tardigrade, aka sea bears) between two different assembling programs through conda. We are following a previous study which used SPAdes as the de novo genome assembler. The assembler we will be comparing to SPAdes is Velvet. Both SPAdes and Velvet are _de novo_ genome assemblers most often used for small genomes and takes paired-ends reads in FASTQ formatting. Velvet was inititally released in 2008, while SPAdes is much newer, having been fully released in 2021. 

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
First, we will be using two separate _De novo_ genome assemblers called Velvet and SPAdes. We are inputting the same Tardigrade forward and reverse reads into each system. Once we have both genome assemblies, we will be using the genome assembly evaluation tool (QUAST) to determine the quality of each assembly from the two separate pipelines.  

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
conda install -c bioconda velvet

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
velveth velvet_assembly 51 -fastq -longPaired -separate forwardreads.trimmed.fastq.gz reversereads.trimmed.fastq.gz
velvetg velvet_assembly/ -min_contig_length 200

```

### QUAST
```
## Installing environment for QUAST
conda create -n quast-env python=3.6
conda activate quast-env

## Running QUAST on SPAdes output
quast.py -o quastoutput contigs.fasta

## Running QUAST on Velvet output
quast.py -o quastoutput2 contigs.fa
```

## RESULTS
### SPAdes
#### Contigs coverage histogram produced after running QUAST on the gnemome allignment produced using SPAdes
![Screen Shot 2022-05-16 at 12 15 05 PM](https://user-images.githubusercontent.com/103778088/168637962-8166970a-97db-44b4-8035-37359151517f.png)

#### Frequency of different contig sizes after running QUAST on the gnemome allignment produced using SPAdes
![Screen Shot 2022-05-16 at 12 15 30 PM](https://user-images.githubusercontent.com/103778088/168637964-932091fe-4b93-4b39-a1aa-684dd4d7a98d.png)

#### GC content histogram for contigs produced using SPAdes genome allignment
![Screen Shot 2022-05-16 at 12 15 37 PM](https://user-images.githubusercontent.com/103778088/168637966-8f12e969-602e-423d-991b-f4de1acef980.png)

#### GC content histogram for windows produced using SPAdes genome allignment
![Screen Shot 2022-05-16 at 12 15 43 PM](https://user-images.githubusercontent.com/103778088/168637967-a38b535a-1d6b-4ebc-8102-ae113146a07f.png)

#### Cumulative contig length line graph produced after running QUAST on the gnemome allignment produced using SPAdes
![Screen Shot 2022-05-16 at 12 15 53 PM](https://user-images.githubusercontent.com/103778088/168637968-8863790a-28d0-463d-859d-adb3f4085cd4.png)

#### Coverage histogram comparing contig coverage depth to coverage length based off of reads produced using SPAdes genome allignment
![Screen Shot 2022-05-16 at 12 16 00 PM](https://user-images.githubusercontent.com/103778088/168637970-c67ca16f-1090-4c96-ba91-56667be51e69.png)

#### General Quality Assesment produced by QUAST based off of reads produced using SPAdes genome allignment
![Screen Shot 2022-05-16 at 3 00 25 PM](https://user-images.githubusercontent.com/103778088/168663474-dde09df1-5d9a-4b98-bc9d-42f52541b653.png)

### Velvet

#### Contigs coverage histogram produced after running QUAST on the gnemome allignment produced using Velvet
![Screen Shot 2022-05-16 at 12 15 05 PM](https://user-images.githubusercontent.com/103778088/168662167-b270e0db-0dfc-47ee-a52f-84fc90c0ec6f.png)

#### Frequency of different contig sizes after running QUAST on the gnemome allignment produced using Velvet
![Screen Shot 2022-05-16 at 12 15 30 PM](https://user-images.githubusercontent.com/103778088/168662165-26514a8b-32f5-4732-b1c4-de88269dac6d.png)

#### GC content histogram for contigs produced using Velvet genome allignment
![Screen Shot 2022-05-16 at 12 15 37 PM](https://user-images.githubusercontent.com/103778088/168662164-bd484a06-8733-4c65-9d38-7d9ddfc5a05b.png)

#### GC content histogram for windows produced using Velvet genome allignment
![Screen Shot 2022-05-16 at 12 15 43 PM](https://user-images.githubusercontent.com/103778088/168662161-38f50146-f491-49e0-b501-ebf74e91edb4.png)

#### Cumulative contig length line graph produced after running QUAST on the gnemome allignment produced using Velvet
![Screen Shot 2022-05-16 at 12 15 53 PM](https://user-images.githubusercontent.com/103778088/168662159-9cb28561-db7d-437a-a408-61a02c7dfc60.png)

#### Coverage histogram comparing contig coverage depth to coverage length based off of reads produced using Velvet genome allignment
![Screen Shot 2022-05-16 at 12 16 00 PM](https://user-images.githubusercontent.com/103778088/168662158-4090ac6a-72ad-4c41-ae8c-8902b2f86b69.png)

#### General Quality Assesment produced by QUAST based off of reads produced using Velvet genome allignment
![Screen Shot 2022-05-16 at 2 55 58 PM](https://user-images.githubusercontent.com/103778088/168663016-d0da52c2-33af-4cde-8016-2f496b722a82.png)

## DISCUSSION

### Comparison of GC% found using QUAST on SPAdes and Velvet alignments as well as previously alligned genomes from NCBI database as reference

| Reference 1  |  Reference 2  | SPAdes |  Velvet |
| :---: | :---: | :---: | :---: |
| 45.3%  | 46.9% | 45.1%  | 45.7%  |

### Comparison of genome length(Mb) found using QUAST on SPAdes and Velvet alignments as well as previously alligned genomes from NCBI database as reference

| Reference 1  |  Reference 2  | SPAdes |  Velvet |
| :---: | :---: | :---: | :---: |
| 182.16  | 252.54 |  1164.47 | 217.49  | 

## CONCLUSION
Bioinformatics genome allignment technology has 
SPAdes



downloading files: 

https://en.wikipedia.org/wiki/De_novo_sequence_assemblers
https://canu.readthedocs.io/en/latest/quick-start.html
https://canu.readthedocs.io/en/latest/tutorial.html#tutorial
https://anaconda.org/bioconda/canu
