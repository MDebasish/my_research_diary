#######################################
# numeric sorting of chromosome names #
#######################################

# EK: mm10 fullgenome downloaded on 13.04.2016 from UCSC (ftp://hgdownload.cse.ucsc.edu/goldenPath/mm1010/bigZips/)

(1) rsync -avzP rsync://hgdownload.cse.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz .

(2) tar -xzvf chromFa.tar.gz

(3) cat *fa > mm10.fa



#######################################
# lexical sorting of chromosome names #
#######################################

# download
wget http://hgdownload.soe.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz

# concatenate fasta files in chr1, chr2, chr3, ... order
# first leave out chrUn
for file in $(ls *fa | grep -v 'chrUn' | sort -V)
 do
 echo $file
 cat $file >> genome.fa
 done
# second include chrUn
for file in $(ls *fa | grep 'chrUn' | sort -V); do  echo $file; cat $file >> genome.fa; done

# index
samtools faidx genome.fa
java -jar /fsimb/groups/imb-bioinfocf/common-tools/dependencies/picard/1.140/picard.jar CreateSequenceDictionary REFERENCE=genome.fa OUTPUT=genome.dict

# rename genome.* to mm10.sorted.*
mv genome.fa mm10.sorted.fa
mv genome.dict mm10.sorted.dict
mv genome.fa.fai mm10.sorted.fa.fai
