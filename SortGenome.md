\###########################################<br />
\## __Numeric sorting of Genome for Chr names__ ##<br />
\##########################################<br />

 mm10 fullgenome downloaded on 13.04.2019 from [UCSC](https://hgdownload.soe.ucsc.edu/downloads.html "UCSC Sequence and Annotation Downloads")
 
 download with `rsync` or `wget`

```bash
rsync -avzP rsync://hgdownload.cse.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz .
wget http://hgdownload.soe.ucsc.edu/goldenPath/mm10/bigZips/chromFa.tar.gz

tar -xzvf chromFa.tar.gz

cat *fa > mm10.fa
```
---
\##########################################<br />
\## __Lexical sorting of Genome for Chr names__ ##<br />
\#########################################<br />

concatenate fasta files in chr1, chr2, chr3, ... order and first leave out chrUn
```bash
for file in $(ls *fa | grep -v 'chrUn' | sort -V)
do
 echo $file
 cat $file >> genome.fa
done

for file in $(ls *fa | grep 'chrUn' | sort -V)
do
 echo $file
 cat $file >> genome.fa
done
```
---
\##########################################<br />
\## __index rename genome.* to mm10.sorted.*__ ##<br />
\#########################################<br />

```bash
samtools faidx genome.fa
java -jar picard/1.140/picard.jar CreateSequenceDictionary REFERENCE=genome.fa OUTPUT=genome.dict

mv genome.fa mm10.sorted.fa
mv genome.dict mm10.sorted.dict
mv genome.fa.fai mm10.sorted.fa.fai
```



```bash
#!/bin/bash
#usage   

SAMTOOLS=$1
BAM_INPUT=$2
PATH_OUTPUT=$3
BAMTOOLS=~/bio/app/bamtools/bin/bamtools/2.3.0/bamtools
#create the target directory
mkdir -p ${PATH_OUTPUT}

if [ -f $BAMTOOLS ];
then

 cd ${PATH_OUTPUT} && ~/bio/app/bamtools/bin/bamtools split -in ${BAM_INPUT} -refPrefix "" -reference
 for i in *.chr*bam 
 do 
  sampleName=${i%.bam}
  chrName=${sampleName#*.}
  ${SAMTOOLS} view -h -F 0x4 -q 10 ${i} | awk '{if(/^@SQ/ && !/^@SQ\tSN:'$chrName'/) next} {if(/^@/) print $0}{if ($3~/^'$chrName'/) print $0}' | ${SAMTOOLS} view -hbS - > ${PATH_OUTPUT}/${sampleName}.tmp
  mv ${PATH_OUTPUT}/${sampleName}.tmp ${PATH_OUTPUT}/${sampleName}.bam
 done
else
 fileName=$(basename "$BAM_INPUT")
 #extension="${fileName##*.}"
 sampleName="${fileName%.*}"
 #split the bam
 for i in `${SAMTOOLS} view -H ${BAM_INPUT} | awk -F"\t" '/@SQ/{print $2}' |  cut -d":" -f2`
 do
  ${SAMTOOLS} view -h -F 0x4 -q 10 ${BAM_INPUT} $i | awk '{if(/^@SQ/ && !/^@SQ\tSN:'$i'/) next} {if(/^@/) print $0}{if ($3~/^'$i'/) print $0}' | $1 view -hbS - > ${PATH_OUTPUT}/${sampleName}.$i.tmp
  mv ${PATH_OUTPUT}/${sampleName}.$i.tmp ${PATH_OUTPUT}/${sampleName}.$i.bam
 done
fi
```

```bash
grep -v ^@ Rawdata/star_Aligned.out.sam |awk '{print $5}' | perl -ne 'chomp;$H{$_}++; END {$cnt += $H{$_} for sort keys %H; print "$_ - $H{$_} - ".(($H{$_}/$cnt)*100)."\n" for sort {$a<=>$b} keys %H; }'
```
