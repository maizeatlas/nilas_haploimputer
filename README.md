# nilas_haploimputer
## Overview of nilas_haploimputer:

NILAS_haploimputer was written in Python 3.6; data frames constructed using Pandas v0.25.0 (McKinney 2010). NILAS_haploimputer selects the donor and recurrent parent genotype matrices from consensus matrix and then parses the corresponding NILAS lines from the genotype extracted VCF; concatenating the founder and NILAS progeny into one genotype matrix in order to:  i) filter heterozygous and non-polymorphic markers from donor/recurrent parents ii) encode NILAS line genotypes according to parent-of-origin based on the remaining homozygous polymorphic markers iii) aggregate consecutive encoded genotypes into haplotype blocks iv) impute missing marker and haplotype blocks based on adjacent contig congruence v) call second alternate alleles vi)
exports genomic coordinates and encoded/imputed genotype matrices for visualization in FlapJack v1.8.0 (Milne et al. 2010) and vii) convert haplotype block marker data into physical coordinates for characterization.

### Parent-of-Origin Encoding:
NILAS_haploimputer.py was written to encode each marker as a recurrent, donor, heterozygous, or missing state using a series of conditions. The script merges the genotype data on the parental lines from the consensus genotype file and the NILAS lines from the VCF file into one genotype matrix. The genotype matrix coordinates are filtered to exclude markers that are heterozygous, missing or non-polymorphic in the parents. The subsetted homozygous-polymorphic parental markers are then used to encode parent-of-origin information for markers scored on the NILAS lines.  
### Genotype Imputation:  
A haplotype block is a genomic region in which markers tend to exhibit strong linkage disequilibrium. Once markers have been encoded by NILAS_haploimputer.py, the script then defines haplotype blocks by iterating through the encoded genotypes; consecutive markers of the same encoded parent-of-origin are aggregated into a haplotype block. A change in parent-of-origin between two adjacent markers is considered a crossover site. Once haplotype blocks are defined, NILAS_haploimputer.py applies a series of conditions to impute missing data and haplotype blocks that are supported by a single contig. 
### Missing Data Imputation:  
The first condition applied by NILAS.py imputes missing marker data by assigning flanking markers to the 5’ and 3’ of each haplotype block. Haplotype blocks defined as missing are then subject to imputation by comparing the encoded genotypes of the flanking markers; if the markers match then the haplotype block is imputed as the genotype of adjacent haplotype block.  
### Marker Coverage Imputation:  
In the VCF filtering stage of analysis markers are selected by identity to in silico restriction enzyme digest cut sites. Pybedtools is utilized to assign RE site identifiers to NILAS markers at intersecting coordinates. The number of unique in silico sites, informs the number of contigs supporting the haplotype block. This config number is weighted in imputation; haplotype block with less than two supporting contigs are imputed to the nearest neighboring haplotype block genotype. 
With the imputation rules applied by NILAS.py, less than 0.1% of the genotype data remains as missing data. The imputed genotypes allow for annotation of introgression lines, providing valuable information on genotype composition, introgression number, recombinant breakpoint resolution, introgression size and position.  


## Dependencies:

* pybedtools <https://daler.github.io/pybedtools/>  >= v0.7.10
* numPY <https://numpy.org/> >= v1.17.0
* pandas <https://pandas.pydata.org/> >= v0.25.0 

## Installation:

`$ git clone git@github.com:maizeatlas/nilas_haploimputer.git`

## Input:
1. Recurrent Parent Consensus Genotype - ex. 2369C
2. Donor Parent Consensus Genotype - ex. CML277C
3. Group Number - ex. g31
4. Directory containing:  

      -NILAS Genotype Extracted VCF file - ex. NILAS_g31.GT  
      -NewCon.txt: Founder Genotype Consenus File  
      -isdigB73v4_pid96_convert.bed: In silico digestion bed file  
      -multimode.txt: multimodal marker sites excluded in Founder genotype consensus file  

**All input files must be tab seperated**

## Output:
1. Group bed file containing marker-in silico-contig intersection coordinates and identifer
2. Encoded genotype matrix for NILAS group 
3. Imputed genotype matrix for NILAS group
4. Genotype matrices per subgroup for introgression and complement lines
5. Genotype Composition per subgroup files for introgression and complement lines
6. Subgroup summary files 

## Usage:

`$ python NILAS_haploimputer.py 2369C CML277C g31 '/Users/Todd/Dropbox/NILAS/' NILAS_g31.GT`
