#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

``` bash
awk -F "\t" '{print NF;exit}' fang_et_al_genotypes.txt
986
du -h fang_et_al_genotypes.txt
wc -l  fang_et_al_genotypes.txt
head fang_et_al_genotypes.txt | column -t -s $'\t' | less -S
grep "abph1.20" fang_et_al_genotypes.txt
```
By inspecting this file I learned that:

1. This file hs 986 column
2. File size is 6.7 M
3. 2783 lines in this file
4. We can see the file in tabulated form by the 4th code here.

or

* point 1
* point 2
* point 3

###Attributes of `snp_position.txt`

``` bash
awk -F "\t" '{print NF;exit}' snp_position.txt
du -h snp_position.txt
wc -l snp_position.txt
head snp_position.txt | column -t -s $'\t' | less -S
```

By inspecting this file I learned that:

1. This file has 15 column
2. File size is 49 K
3. There are 984 lines in this file
4. We can see the SNP ID, Marker ID, Chromosome, position etc from the code

or

* point 1
* point 2
* point 3

##Data Processing

###Maize Data

```bash

```

Here is my code and a brief description of what this code does
# Filter for maize groups
awk 'NR==1 || $3 ~ /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt > maize_genotypes.txt

# Transpose the data
awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
# Join the data
# transposed_maize_genotypes
# Extract the header to a separate file
head -n 1 transposed_maize_genotypes.txt > header.txt

# Sort the file excluding the header
tail -n +2 transposed_maize_genotypes.txt | sort -k1,1 > sorted_data.txt

# Concatenate the header and the sorted data
cat header.txt sorted_data.txt > sorted_transposed_maize_genotypes.txt
# snp_position data
head -n 1 snp_position.txt > snp_header.txt
tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp_data.txt
cat snp_header.txt sorted_snp_data.txt > sorted_snp_position.txt
# join the files
join -1 1 -2 1 -t $'\t' <(tail -n +2 sorted_snp_position.txt) <(tail -n +2 sorted_transposed_maize_genotypes.txt) > joined_data.txt
# Get headers from both files
head -n 1 snp_position.txt > header_snp.txt
head -n 1 transposed_maize_genotypes.txt > header_maize.txt

# Combine headers, assume both files have 'SNP_ID' as the first column
echo -e "$(cat header_snp.txt)\t$(cut -f2- header_maize.txt)" > combined_header.txt
# Combine Header with Joined Data
cat combined_header.txt joined_data.txt > final_joined_maize_data.txt
# To follow instructions for placing the column headers in its space
 cut -f1,3- final_joined_maize_data.txt > modified_joined_maize_data.txt

# 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)
awk -F"\t" 'NR == 1 || $3 == "unknown"' modified_joined_maize_data.txt > maize_SNPs_unknown_positions.txt
# 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)
awk -F"\t" 'NR == 1 || $3 == "multiple"' modified_joined_maize_data.txt > maize_SNPs_multiple_positions.txt

# 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol
{ head -n 1 modified_joined_maize_data.txt; awk -F"\t" '$2 ==3 = "-"} {print}'; } > maize_chr_1_inc.txt

# Change the Chromosome number each time for 10 chromosomeand the output file based on chromosome number

# 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol:
awk -F"\t" 'NR == 1 || $2 == "1"' modified_joined_maize_data.txt | sort -k3,3nr | awk -F"\t" -v OFS="\t" 'NR == 1 || $3 != "0" {print $0; next} { $3 = "?"; print }' > maize_chr_1_dec.txt

# Change the Chromosome number each time for 10 chromosomeand the output file based on chromosome number


###Teosinte Data

```
bash
```

Here is my brief description of code and what this code does
# Join the data
# transposed_teosente_genotypes
# Extract the header to a separate file
head -n 1 transposed_teosinte_genotypes.txt > header.txt	

# Sort the file excluding the header
tail -n +2 transposed_teosinte_genotypes.txt | sort -k1,1 > sorted_data.txt

# Concatenate the header and the sorted data
cat header.txt sorted_data.txt > sorted_transposed_teosinte_genotypes.txt
# snp_position data
head -n 1 snp_position.txt > snp_header.txt
tail -n +2 snp_position.txt | sort -k1,1 > sorted_snp_data.txt
cat snp_header.txt sorted_snp_data.txt > sorted_snp_position.txt
# join the files
join -1 1 -2 1 -t $'\t' <(tail -n +2 sorted_snp_position.txt) <(tail -n +2 sorted_transposed_teosinte_genotypes.txt) > joined_data.txt
# Get headers from both files
head -n 1 snp_position.txt > header_snp.txt
head -n 1 transposed_teosinte_genotypes.txt > header_teosinte.txt

# Combine headers, assume both files have 'SNP_ID' as the first column
echo -e "$(cat header_snp.txt)\t$(cut -f2- header_maize.txt)" > combined_header.txt
# Combine Header with Joined Data
cat combined_header.txt joined_data.txt > final_joined_teosinte_data.txt
# To follow instructions for placing the column headers in its space
 cut -f1,3- final_joined_teosinte_data.txt > modified_joined_teosinte_data.txt

# 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)
awk -F"\t" 'NR == 1 || $3 == "unknown"' modified_joined_teosinte_data.txt > teosinte_SNPs_unknown_positions.txt
# 1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)
awk -F"\t" 'NR == 1 || $3 == "multiple"' modified_joined_teosinte_data.txt > teosinte_SNPs_multiple_positions.txt
# 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol
{ head -n 1 modified_joined_teosinte_data.txt; awk -F"\t" '$2 ==3 = "-"} {print}'; } > teosinte_chr_1_inc.txt
# Change the Chromosome number each time for 10 chromosome and the output file based on chromosome number


# 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol:
{   head -n 1 modified_joined_teosinte_data.txt;   awk -F"\t" '$2 == "1"' modified_joined_teosinte_data.txt | sort -k3,3nr | awk -F"\t" -v OFS="\t" '$3 == "0" {$3 = "-"} {print}'; } > teosinte_chr_1_dec.txt

# Change the Chromosome number each time for 10 chromosome and the output file based on chromosome number


