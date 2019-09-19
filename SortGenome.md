\#######################################<br />
\# numeric sorting of chromosome names #<br />
\#######################################<br />

 mm10 fullgenome downloaded on 13.04.2019 from UCSC (ftp://hgdownload.cse.ucsc.edu/goldenPath/mm10/bigZips/)
 download with `rsync` or `wget`

```bash
rsync -avzP rsync://hgdownload.cse.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz .
wget http://hgdownload.soe.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz

tar -xzvf chromFa.tar.gz

cat *fa > mm10.fa
```

\#######################################
\# lexical sorting of chromosome names #
\#######################################

concatenate fasta files in chr1, chr2, chr3, ... order and first leave out chrUn
```bash
for file in $(ls *fa | grep -v 'chrUn' | sort -V)
 do
 echo $file
 cat $file >> genome.fa
for file in $(ls *fa | grep 'chrUn' | sort -V); do  echo $file; cat $file >> genome.fa; done
```
\##########################################
\# index rename genome.* to mm10.sorted.* #
\##########################################

```bash
samtools faidx genome.fa
java -jar picard/1.140/picard.jar CreateSequenceDictionary REFERENCE=genome.fa OUTPUT=genome.dict

mv genome.fa mm10.sorted.fa
mv genome.dict mm10.sorted.dict
mv genome.fa.fai mm10.sorted.fa.fai
```
