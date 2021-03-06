In order to explore whether other fruits have microbial signatures, and whether metabolite data correlates with microbial content, khmer streaming was used to investigate microbial content.

Yun et al. 2016 (DOI: 10.1038/srep19356) produced duplicate transcriptome reads for litchi fruit, as well as metabolite data, for various time points after litchi fruit were picked.

As such, khmer streaming was used to simultaneously download the 1M reads of the 10 samples and calculate sourmash signatures. sourmash sbt_gather was then run with fungal genomes and with microbial genomes to assess the presence and amount of microbes. 

All analyses were run on the sourmash instance, for which installation instructions can be found here (https://github.com/taylorreiter/olive_public_seq/tree/master/RNA-seq/many_fruit/sourmash).

Install syrah:
```
pip install https://github.com/dib-lab/khmer/archive/master.zip
```

Set up folder system
```
mkdir stream_fruit
cd stream_fruit
mkdir litchi
cd litchi
```

Install SRA toolkit according to these instructions: https://github.com/taylorreiter/olive_public_seq/blob/master/RNA-seq/PRJNA209941/eel_pond/1.SRA_download.md

Run syrah
loop broken (`long int too long to convert`), use single until loop is fixed.
```
for SRA_ID in  SRR1929301 SRR1929302 SRR1929303 SRR1929304 SRR1929305 SRR1929306
do
  ~/sratoolkit.2.8.1-3-ubuntu64/bin/fastq-dump.2.8.1-3 -A $SRA_ID -Z | trim-low-abund.py --ignore-pairs --variable-coverage -M 7e9 -o - - | sourmash compute - --scaled 1000 -o ${SRA_ID}_abundtrim.sig
done
```

Already done:
SRR1929297 SRR1929298 SRR1929299 SRR1929300


Run sbt_gather
```
source ~/sourmashEnv2/bin/activate
for infile in *sig
do
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/fungal_4.13.17.sbt.json --threshold=0.001 -o fungal_k31_${infile}.txt ${infile}
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/microbes.k31.sbt.json --threshold=0.001 -o microbe_k31_${infile}.txt ${infile}
done
```
```
for infile in SRR1929301_abundtrim.sig  SRR1929302_abundtrim.sig SRR1929303_abundtrim.sig SRR1929304_abundtrim.sig SRR1929305_abundtrim.sig SRR1929306_abundtrim.sig
do
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/fungal_4.13.17.sbt.json --threshold=0.001 -o fungal_k31_${infile}.txt ${infile}
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/microbes.k31.sbt.json --threshold=0.001 -o microbe_k31_${infile}.txt ${infile}
done
```
Next, repeat the same procedure for watermelon. 

```
cd /mnt/work/syrah_fruit
mkdir watermelon
cd watermelon
```

Run syrah
```
for SRA_ID in SRR1001435 SRR1001436 SRR1001437 SRR1001438 SRR1001439 SRR1001440 SRR1001441 SRR1001442
do
  ~/sratoolkit.2.8.1-3-ubuntu64/bin/fastq-dump.2.8.1-3 fastq-dump -A $SRA_ID -Z | syrah -k 31 | sourmash compute - -o ${SRA_ID}_syrah.sig
done
```

Run sbt_gather
```
source ~/sourmashEnv2/bin/activate
for infile in *sig
do
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/fungal_4.13.17.sbt.json --threshold=0.001 -o fungal_k31_${infile}.txt ${infile}
  sourmash sbt_gather -k 31 ~/sourmash_SBTs/microbes.k31.sbt.json --threshold=0.001 -o microbe_k31_${infile}.txt ${infile}
done
```
