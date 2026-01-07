##EXPLANATION

#DATA CLEANING
The cleaning script renames the long original names of the samples in the VCF file (Merged.vcf.gz) to the shortened version used in the excel file. The output VCF file is used for further analysis (Merged_clean.vcf.gz).
When running (can be performed without job) make sure to load modules.

#GWAS PROPER
First line is set to stop the script if any command fails.
Following is the job configuration. all SBATCH lines.

Then current versions of tools are loaded (Marias script had some outdated once I believe).
The input file is defined as Merged_clean.vcf.gz

Next the sample names were cleaned once more (I left in a _ in the original names which GEMMA and PLINK can't handle) and replaced with a version without underscore.
Next the phenotype file is named.  Phenotype contained the columns FID IID (both the name of the clonal line stylised as A1C12 for example) and Phenotype, which was the average number of immobilised individuals for that clonal line. 
!note! a text file made from an excel file can't be directly used for the GWAS as there is some weird separator problem it's a quick fix but not included in this script (to unix)
Quality control before kinship matrix is turned of but optionally can be turned on.

Next plink and gemma are loaded.
VCF is converted into plink
awk block checks phenotype file and only keeps individuals with phenotype (is not really problem for this file as all FID/IID have phenotype)
next a subset is made where only genotypes are kept with phenotypes
phenotypes are added in 1 file and form the file gwas_input_merged
plink imiss is just diagnostics

Quality control is turned off butcan exclude individuals >10% missing genotype, SNPs >5% missing, Minor allelefrequency >1%

Next a kinship matrix is made to account for population structure and relatedness
Next is the GWAS, LMM, no covariates
output gemma_lmm_results_nocov

#LENGTH SCAFFOLDS
to make the manhattan plots the number and length of scaffolds is needed. this simple script extracts this information from the VCF which unfortunately first needs to be unzipped

#MANHATTAN
This plot is made to create a Manhattan plot in SLURM script on the supercomputer, Manhattan in R and Manhattan light do the same except directly in R.
manhattan light doesn't plot all points to make it less computationally intensive (my computer struggled to make these plots)

First job configuration, command to stop if any command fails (this is not finetuned at all way to heavy probably)
load R+ packages
make some parameters (now accepts previously generated files)

process scaffold lengths
compute coordinates for plotting based on this length

load gwas results: scaffold, position within scaffold and p-value
convert to to cumulative positions on genome (each SNP coordinate)
calculate treshold
optional downsampling (plot not all SNP but now defined as 1*10^5)
alternating colors for scaffolds
plot in png
