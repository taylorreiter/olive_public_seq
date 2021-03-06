Calculate the genome signature of each of the scaffolds in Oe6. See if any scaffolds differ, and if so, if these are the same scaffolds that were identified by BLAST as having fungal content.

calculate di, tri, tetra, and penta nucleotide frequencies of olive scaffolds. Use scaled 10kbp; resolution not necessary.
```
cd /mnt/work/ref_genome
mkdir scaffolds
cd scaffolds
ln -s ../Oe6.scaffolds.fa .

awk '/>Oe6_s/{n++}{print > n ".scaffold.fa" }' Oe6.scaffolds.fa

for infile in *scaffold.fa
do
  sourmash compute -k 2,3,4,5 --scaled 5 --track-abundance -o ${infile}.s5.k2-5.sig ${infile}
done
```

Sigs keep crashing jupyter notebook. Remove fasta files from directory
```
mkdir fastas
for infile in *.fa
do
  mv $infile ./fastas/$infile
done
```

Still crashed, run compare on the command line
```
sourmash compare -k 4 -o Oe6_scaffolds_k4.comp *sig
sourmash compare -k 2 -o Oe6_scaffolds_k2.comp *sig
sourmash compare -k 3 -o Oe6_scaffolds_k3.comp *sig
sourmash compare -k 5 -o Oe6_scaffolds_k5.comp *sig
```


Other tools that can be used: SigHunt, BLSOM ProDeGe


In python interpretter:
```
import matplotlib
matplotlib.use('Agg')
from sourmash_lib import fig
import pandas as pd
import numpy
D, labels = fig.load_matrix_and_labels("Oe6_scaffolds_k4.comp")
labels[0]
data = pd.DataFrame(D, index=labels, columns=labels)
import seaborn as sns
graph2 = sns.clustermap(data, col_cluster = True, figsize = (190, 190))
graph2.savefig('Oe6_scaffolds_k4_comp.svg')
```

Install branch of sourmash that outputs compare matrix as a csv. Re-run, and save matrices as csv; try and plot with R
```
git clone https://github.com/dib-lab/sourmash/ --branch output/compare_matrix
cd sourmash
make

cd ..
./sourmash/sourmash compare -k 4 --csv Oe6_scaffolds_k4.comp.csv *sig
./sourmash/sourmash compare -k 2 --csv Oe6_scaffolds_k2.comp.csv *sig
./sourmash/sourmash compare -k 3 --csv Oe6_scaffolds_k3.comp.csv *sig
./sourmash/sourmash compare -k 5 --csv Oe6_scaffolds_k5.comp.csv *sig
```

In R, make a cluster of distance matrix to visualize output.
```
Oe6_scaffolds_k4<-read.csv("Oe6_scaffolds_k4.comp.csv", header = T)
library(qgraph)
png('Oe6_scaffolds_k4.comp.png', width=4000, height=4000, unit='px')
qgraph(Oe6_scaffolds_k4, layout='spring', vsize=3)
dev.off()
# This failed to finish in 15 hours with over 100gb ram. Exit.
```
Try a different approach in R, and install fastclust package to speed up process
```
install.packages("fastcluster")
library(fastcluster)
compk4<-read.csv("Oe6_scaffolds_k4.comp.csv")
rownames(compk4)<-colnames(compk4)
cluster_compk4<-hclust(dist(compk4), "cen")
compk4_clusters<-hclust(dist(compk4))
```

```
k <- 11
cols <- rainbow_hcl(k)
dend <- as.dendrogram(compk4_clusters)
#dend <- color_branches(dend, k = k)
#plot(dend)
labels_dend <- labels(dend)
groups <- cutree(dend, k=11, order_clusters_as_data = FALSE)
# absurd an slow, exited without continuing.

dends <- list()
for(i in 1:k) {
    labels_to_keep <- labels_dend[i != groups]
    dends[[i]] <- prune(dend, labels_to_keep)
}

#par(mfrow = c(2,2))
#for(i in 1:k) {
#    plot(dends[[i]],
#        main = paste0("Tree number ", i))
#}

png('Oe6_scaffolds_k4_dend1.comp.png', width=1000, height=1000, unit='px')
plot(dends[[1]], main = "Tree number 1")
dev.off()
```

TL;DR needs data reduction before visualization.
