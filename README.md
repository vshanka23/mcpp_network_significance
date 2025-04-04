# Description
This is a repository for code and some databases used to test the significance of the architecture and complexity of an observed network derived from a database of associations. We use average of the number of connections (degree) within the observed network as proxy to define 'architecture and complexity'. This metric can be changed if an argument could be made that a different calculation or index is a better estimate. The R script calculates the connectivity index (average degree) for the observed network, then compares it to a null distribution of connectivity indices generated from randomly sampled networks from the database such that each random network has the same number of nodes as the observed network.

#  Prerequisites
## Dependencies

 - [R >4.0.0](https://cran.r-project.org) 
 - [igraph library](https://r.igraph.org)

## Inputs
The R script requires two inputs:
 1. A database network as a TAB-delimited text file with a header and the following format:
```
InteractorA	InteractorB
GALM	GCK
NCAPD3	KIF4A
SKIV2L	DHX38
TAF4	HNRNPA1
JMJD6	DEK
NR3C1	SMARCE1
PECAM1	EDN1
HTR5A	RIT2
TBPL1	POLR1C
LDHAL6A	CKB
```
 2. A list of nodes that represents your observed network as a TAB-delimited text file with a header:
```
shared
PPIB
RPS20
TLN2
WWC1
CSPG4
SORBS1
FLT1
PBX3
ETS1
PRKCI
FOXO3
```
# Instructions
## Running the script
1. Clone this repo to your local compute environment.
2. Choose the number of permutation you want to use. We recommend 10,000 for complex networks.
3. Run this either in a SLURM sbatch script like this:
```
#!/bin/bash
#
#SBATCH --job-name=MCPP
#SBATCH --cpus-per-task=1
#SBATCH --partition=<partition name>
#SBATCH --time=1:00:00
#SBATCH --mem=4G
#SBATCH --output=<path_to_store_standard_output>/MCPP.%j.out
#SBATCH --error=<path_to_store_standard_error>/MCPP.%j.err
#SBATCH --mail-type=all
#SBATCH --mail-user=<email>

module load R/4.1.2

cd <path to working directory>
Rscript \
	<path to this git repo from step 1>/mcpp.R \
	<database network file> \
	<list of nodes from the observed network> \
	<number of permutations>
```
Or run interactively like this:
```
module load R/4.1.2

Rscript \
	<path to this git repo from step 1>/mcpp.R \
	<path to database network file> \
	<path to list of nodes from the observed network> \
	<number of permutations>
```

Here is an example of an actual invoke:
```
module load R/4.1.2
cd ~
Rscript \
	mcpp_network_significance/mcpp.R \
	mcpp_network_significance/d_mel_flybase_interaction_2024_02_phy.txt \
	mcpp_network_significance/Diff_network_genes.txt \
	10000
```
# Results
## Outputs
There are two main outputs:
1. Standard Output (to file or console) with the Observed Connectivity Index, its *p*-value calculated as the ratio between the rank of the observed value and the total number of permutations, and some summary statistics of the null distribution:
```
Observed Connectivity Index: 3.666667
P-value: 0
Summary of null distribution:
Min. 1st Qu.  Median  Mean 3rd Qu.  Max.
0.00000 0.08889 0.13333 0.15229 0.20000 0.64444
Standard deviation of null distribution: 0.07963622
null device
1
```
2. A TIFF file named ```hist_kde_overlay.tiff```  in the current working directory with the histogram of the null distribution, a Kernel Density Estimation (KDE) graph overlayed on the histogram and the Observed Connectivity Index (same value as what is show in the Standard Output) in the legend. You will see a vertical red line representing the Observed Index value in the plot if the value is within the range (between min and max) of the null distribution.

![histogram](https://github.com/vshanka23/mcpp_network_significance/blob/main/hist_kde_overlay.jpg)

# Helpful resources
## Database Networks
As part of the repo, I have provided some preconstructed databases for *Drosophila melanogaster* and *Homo sapiens*
1. ```d_mel_flybase_interaction_2024_02_all.txt```is the [FlyBase Interaction Database](https://flybase.org/downloads/bulkdata) version 2024_02 consisting of both genetic and physical interactions.
2. ```d_mel_flybase_interaction_2024_02_phy.txt``` is the same as above but filtered for physical interactions only.
3.  ```d_mel_flybase_interaction_2024_02_gen.txt``` is the same as above but filtered for genetic interactions only.
4. ```h_sap_string-db_11.5_CS500.txt``` is the protein-protein interaction database for *H.sapiens* from [STRING-db](https://string-db.org/cgi/download?sessionId=b1QJliUQu3nf&species_text=Homo+sapiens&settings_expanded=0&min_download_score=0&filter_redundant_pairs=0&delimiter_type=txt) v11.5 that has been filtered for combined scores of 500 and above.
## Test files
1. ```test_network_genes.txt``` is a test file consisting of a set of *Drosophila* genes that results in a strongly contigious/complex network. This file can be used with ```d_mel_flybase_interaction_2024_02``` ```all,phy,```or```gen``` database networks from above to test the R script. 
 2. ```test_random_genes.txt``` is a test file that consists of a random set of *Drosophila* genes. This file can be used with ```d_mel_flybase_interaction_2024_02``` ```all,phy,```or```gen``` database networks to show negative control.

# Acknowledgments
[Katelynn Collins](https://github.com/kcolli5) and [Elisa Howansky](https://github.com/ehowans) were instrumental in testing the script, generating test data and providing feedback on both usage and visualizations.