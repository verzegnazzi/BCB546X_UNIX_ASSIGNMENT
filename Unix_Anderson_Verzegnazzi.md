
# UNIX Assignment _ Sept 21, 2018

## Name: Anderson Verzegnazzi#
___


# 1. Data Inspection
______

$ pwd

$c/Users/verze

$ cd BCB546X_UNIX_ASSIGNMENT

$ ls

fang_et_al_genotypes.txt 

README.md

snp_position.txt

$ wc fang_et_al_genotypes.txt

	N. of lines:2783  N. of words:2744038 N. bytes: 11051939

$ wc snp_position.txt

  	N. of lines:984  N. of words:13198  N. bytes: 82763

$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt

	986 columns

$ awk -F "\t" '{print NF; exit}' snp_position.txt

	15 columns

$ du -h fang_et_al_genotypes.txt

	11M

$ du -h snp_position.txt

	84K



# 2. Processing of the data

### 2.1 Grouping data from fang_et_al_genotypes.txt  for Maize and Teosinte:

- Maize (Group = ZMMIL, ZMMLR, and ZMMMR)


		$ grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt


- Teosinte (Group = ZMPBA, ZMPIL, and ZMPJA)

		$ grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt


###2.2 Extract header from the original file and add it to the extracted

	$ grep "Group" fang_et_al_genotypes.txt > header.txt
 	$ cat header.txt maize_genotypes.txt > maize_header.txt 
 	$ cat header.txt teosinte_genotypes.txt > teosinte_header.txt

### 2.3 Remove *Sample ID* and *JG_OTU* for having common column (SNP ID) in  the join the files

 	$ cut -f 3-968 maize_header.txt > maize_cut.txt 
 	$ cut -f 3-968 teosinte_header.txt > teosinte_cut.txt

### 2.4) Transpose the data for havinf the SNP-ID in the first column in both file

 	$ awk -f transpose.awk maize_cut.txt > transposed_maize.txt
 	$ awk -f transpose.awk teosinte_cut.txt > transposed_teosinte.txt

### 2.5 Creating files with column 1,3 and 4 and removing headers

	$ grep -v "^#" snp_position.txt | cut -f 1,3,4 > snp_position_cut.txt

	$ grep -v "Group" transposed_maize.txt > maize_no_header.txt $ grep -v "Group" transposed_teosinte.txt > teosinte_no_header.txt $ grep -v "SNP_ID" snp_position_cut.txt > snp_no_header.txt

### 2.6 Sort and join the new files

	$ sort -k1,1 maize_no_header.txt > maize_sorted.txt $ sort -k1,1 teosinte_no_header.txt > teosinte_sorted.txt $ sort -k1,1 snp_no_header.txt > snp_sorted.txt

	$ join -t $'\t' -1 1 -2 1 snp_sorted.txt maize_sorted.txt > maize_joined.txt $ join -t $'\t' -1 1 -2 1 snp_sorted.txt teosinte_sorted.txt > teosinte_joined.txt

### 2.7 Files by chromosome 

	$ for i in {1..10}; do awk '$2=='$i'' maize_sorted_by_chr.txt > maize_chr"$i"_question_mark.txt; done $ for i in {1..10}; do awk '$2=='$i'' maize_dash_mark.txt > maize_chr"$i"_dash_mark.txt; done

	$ for i in {1..10}; do awk '$2=='$i'' teosinte_sorted_by_chr.txt > teosinte_chr"$i"_question_mark.txt; done $ for i in {1..10}; do awk '$2=='$i'' teosinte_dash_mark.txt > teosinte_chr"$i"_dash_mark.txt; done



### 2.8 10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?

	$ for i in {1..10}; do sort -k3,3n maize_chr"$i"_question_mark.txt > maize_chr"$i"_question_mark_final.txt; done
	$ for i in {1..10}; do sort -k3,3n teosinte_chr"$i"_question_mark.txt> teosinte_chr"$i"_question_mark_final.txt; done

### 2.9 10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -

	$ for i in {1..10}; do sort -k3,3nr maize_chr"$i"_dash_mark.txt > maize_chr"$i"_dash_mark_final.txt; done
	$ for i in {1..10}; do sort -k3,3nr teosinte_chr"$i"_dash_mark.txt > teosinte_chr"$i"_dash_mark_final.txt; done

### 2.10 1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

	$ grep "unknown" maize_sorted_by_chr.txt > maize_unknown_position.txt $ grep "unknown" teosinte_sorted_by_chr.txt > teosinte_unknown_position.txt

### 2.11  1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

	$ grep "multiple" maize_sorted_by_chr.txt > maize_multiple_position.txt $ grep "multiple" teosinte_sorted_by_chr.txt > teosinte_multiple_position.txt
