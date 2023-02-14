# Bacterial_Abundance_Metaphlan
Procedure for determining the bacterial abundances using Metaphlan with shotgun metagenomics reads




BIOINFORMATIC WORKSHOP 1

BACTERIAL COMMUNITY ABUNDANCE FROM WHOLE SHOTGUN METAGENOMIC SEQUENCES

Table of contents

1.	Reception or download of read files
2.	Checking the integrity of the read files
3.	Renaming your files for more convenience
4.	Read filtering, trimming and quality reports
5.	Filtering out unwanted reads (contaminants or host reads)
6.	Subsampling reads (optional, case dependant) 
7.	Getting the bacterial abundances

Workflow overview
 
<img width="524" alt="image" src="https://user-images.githubusercontent.com/125351299/218715023-97519d32-2ea5-4077-b0b3-0b4ffa00df6f.png">


Important note:

I recommend using the text editor "Sublime" to open, edit and view different script or codes. It can recognize the different coding languages and will colour the codes to help you distinguish the different elements of the code. Example for the bash language:

<img width="487" alt="image" src="https://user-images.githubusercontent.com/125351299/218717383-c484b70f-44ad-404a-ba35-82aaf58db079.png">

 


#

1. Reception or download of the reads from the sequencing facility


Example of file list (gz means they are compressed to save space)

 <img width="191" alt="image" src="https://user-images.githubusercontent.com/125351299/218717575-db7bd61c-a77f-451e-aa36-fc09c8dbe54b.png">


Why do we get two files for each sample?

 


What is inside a FASTQ file?

4 lines for each read (can contains millions of reads)

 
<img width="321" alt="image" src="https://user-images.githubusercontent.com/125351299/218717635-aab09855-bda8-4a62-ae45-c8faca411917.png">


What information is in the identifier? 


 <img width="220" alt="image" src="https://user-images.githubusercontent.com/125351299/218717730-47ee4513-e10c-4627-8c5f-54689a309d89.png">

#

2. Checking the integrity or damage of the files with md5sum

There is a slight possibility that the files you have received or downloaded might be damaged or corrupted. If they are, they could create errors during the following steps, or even produce false results without you noticing anything. 

That's why it is important to check file integrity, which is usually done using what we call the md5sum.

The md5sum corresponds to a unique code that was given to each file when it was first created. If there is the tiniest difference in that file after a download or transfer, the md5sum will be different. If you get two different md5sum for a same file, it means that it is certainly corrupted/damaged.

<img width="524" alt="image" src="https://user-images.githubusercontent.com/125351299/218717916-8486b174-1154-43de-9f55-8d3c844d31a8.png">

 
Example command: 


$ md5sum --check list_with_sums.txt

#

3. Renaming your files for more convenience in the future...

Working with only few samples vs lot of samples...

Loops or no loops...




Example command on a MAC system: 

TESTING WITHOUT CHANGING NAMES FOR REAL!

for f in *L1_1.fq.gz do echo mv "$f" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_1*.fq.gz/R1_001.fastq.gz}"; done


for f in *L1_2.fq.gz; do echo mv "$f" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_2*.fq.gz/R2_001.fastq.gz}"; done


NOW CHANGING NAMES FOR REAL, BE CAREFUL !


for f in *L1_1.fq.gz; do mv "$f" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_1*.fq.gz/R1_001.fastq.gz}"; done


for f in *L1_2.fq.gz; do mv "$f" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_2*.fq.gz/R2_001.fastq.gz}"; done 

#

4. Read trimming and quality reports

Principle

 <img width="316" alt="image" src="https://user-images.githubusercontent.com/125351299/218718565-ac0497f7-8333-4fe3-b62c-4a69b26dee82.png">


 ![image](https://user-images.githubusercontent.com/125351299/218718609-35779397-1a97-4210-b2ef-a95940d62a8c.png)



Programs needed:

FASTP
MULTIQC

Example commands: 

mkdir CLEANED_READS
mkdir REPORTS_CLEANED_READS


for prefix in $(ls *.fastq.gz | sed -E 's/_R[12]_001[.]fastq.gz//' | uniq)
do
echo "performing cleaning on files :" "${prefix}_R1_001.fastq.gz" "${prefix}_R2_001.fastq.gz"
fastp -i "${prefix}_R1_001.fastq.gz" -I "${prefix}_R2_001.fastq.gz" -o CLEANED_READS/"${prefix}_FASTP_R1_001.fastq.gz"  -O CLEANED_READS/"${prefix}_FASTP_R2_001.fastq.gz" --report_title "${prefix}_fastp_report" --thread 10 -j REPORTS_CLEANED_READS/"${prefix}_fastp".json -h REPORTS_CLEANED_READS/"${prefix}_fastp".html
done

multiqc ./REPORTS_CLEANED_READS/ --ignore-symlinks --outdir ./REPORTS_CLEANED_READS --filename MULTIQC_ALL_SAMPLE_REPORT --fullnames --title MULTIQC_ALL_SAMPLE_REPORT







#

5. Filtering out unwanted reads (contaminants or host reads)

Principle :

 <img width="360" alt="image" src="https://user-images.githubusercontent.com/125351299/218718771-821e432e-0e13-400d-85c4-e40949c359bf.png">



Programs needed

BOWTIE2
FASTQC
MULTIQC

Example commands: 

for file in *.fasta
do 
bowtie2-build --threads 10 $file $file.INDEX
done


mkdir FASTQC_ANALYSIS

for index in *.fasta
do
#Create directory for future file storage
mkdir ${index}.INDEX

	# Loop for alignment of each read PE files on each genome (index)
	for prefix in $(ls *.fastq.gz | sed -E 's/_R[12]_001[.]fastq.gz//' | uniq)
	do
	echo "performing alignment on files :" "${prefix}_R1_001.fastq.gz" "&" "${prefix}_R2_001.fastq.gz" "ON INDEX :" "${index}"
	bowtie2  --very-sensitive-local --un-conc-gz ./"${index}.INDEX/HOST_EXTRACTED_${prefix}_R%_001.fastq.gz" -p 10 -x $index.INDEX -1 "${prefix}_R1_001.fastq.gz" -2 "${prefix}_R2_001.fastq.gz" -S ./"${index}.INDEX"/"${prefix}_${index}.sam" 2>./"${index}.INDEX"/"${prefix}_${index}_BOWTIE2.log"
	rm ./"${index}.INDEX"/"${prefix}_${index}.sam"
	done
fastqc *.fastq.gz -o ./"${index}.INDEX"/FASTQC_ANALYSIS
fastqc ./"${index}.INDEX"/*.fastq.gz -o ./FASTQC_ANALYSIS
done


multiqc ./FASTQC_ANALYSIS/* --ignore-symlinks --outdir ./FASTQC_ANALYSIS/MULTIQC_FILES --filename ALL_REPORTS_MULTIQC --fullnames --title ALL_REPORTS_MULTIQC

multiqc ./"${index}.INDEX"/* --ignore-symlinks --outdir ./FASTQC_ANALYSIS/MULTIQC_MAPPING_INFOS --filename MULTIQC_MAPPING_INFOS --fullnames --title MULTIQC_MAPPING_INFOS

 

6. Subsampling reads (optional, case dependant)


Example commands: 


mkdir ./SUBSAMPLED_READS

for prefix in $(ls *.fastq.gz | sed -E 's/_001[.]fastq.gz//' | uniq)
	do
	echo "Subsampling your file :" "${prefix}_001.fastq.gz"
	seqtk sample -s 10 "${prefix}_001.fastq.gz" 1000000 > ./SUBSAMPLED_READS/SUB_1MILLION_"${prefix}_001.fastq"
done



7. Getting the bacterial abundances


 
Example commands: 

mkdir METAPHLAN_ANALYSIS/

for prefix in $(ls *.fastq.gz | sed -E 's/_FASTP_R[12]_001[.]fastq.gz//' | uniq)
do
    echo "Performing metaphlan3 analysis on sample": "${prefix}_FASTP_R1_001.fastq.gz"
    metaphlan "${prefix}_FASTP_R1_001.fastq.gz" --index mpa_vJan21_CHOCOPhlAnSGB_202103 --bowtie2db /Users/julian/Bioinfo_Local/metaphlan4_DATABASES --nproc 10 --no_map -t rel_ab_w_read_stats --unclassified_estimation --input_type fastq -o METAPHLAN_ANALYSIS/"${prefix}"_metaphlan4_abundance.txt
done

#Then to merge the profiled samples to put them all together :

merge_metaphlan_tables.py METAPHLAN_ANALYSIS/*_metaphlan4_abundance.txt > METAPHLAN_ANALYSIS/MERGED_abundance_table.txt




Additional material

