# :hedgehog:  Bacterial_Abundance_Metaphlan  :hedgehog:


This is a procedure to obtain bacterial abundances using Metaphlan with shotgun metagenomic reads.


### Table of contents (main steps of the procedure)

1.	Reception or download of the reads files
2.	Checking the integrity of the reads files
3.	Renaming your files for more convenience
4.	Quality control of the reads (Reads trimming and quality reports)
5.	Taking out unwanted reads (contaminants or host reads)
6.	Subsampling reads to have the same amount of reads for each sample (usually not recomended, case dependant) 
7.	Getting the bacterial abundances/profiles for each samples

## Workflow overview
 
<img width="800" alt="image" src="https://user-images.githubusercontent.com/125351299/218715023-97519d32-2ea5-4077-b0b3-0b4ffa00df6f.png">


### Important note 1

For this tutorial, you will need to work with the terminal (ideally on Mac on Linux system)

Also, if you are working on your own computer, you might need to install different programs required for the procedure.

We will try to make it as easy as possible by installing all programs through Anaconda.

This means you will first have to install Anaconda (which is somewhat a big program). Then, Anaconda will allow you to install almost everything else you need for any tutorial you can find here.

Find the right Anaconda version for your system there (https://docs.anaconda.com/anaconda/install/index.html)

If you need help to install Anaconda or other software, you can ask me.

I suggest you go through the tutorial and identify the program you need to install. Search for these signs : ⚠️ PROGRAMS TO INSTALL 💻 

The command you will actually need to write in the terminal or adapt for your analyses will be indicated by : ⚠️ TEMPLATE COMMANDS ⌨️

### Important note 2

I recommend using the text editor "Sublime" to open, edit and view different script or codes. It can recognize the different coding languages and will colour the codes to help you distinguish the different elements of the code. 

You can download it here (choose the proper version for your system) : https://www.sublimetext.com/3

Example of what you will get if you write bash language in Sublime (you save your file to .sh format to make the colours appear) :

<img width="800" alt="image" src="https://user-images.githubusercontent.com/125351299/218717383-c484b70f-44ad-404a-ba35-82aaf58db079.png">

 
<br/>


### STEP 1. RECEPTION OR DOWNLOAD OF THE READS FILES FROM THE SEQUENCING FACILITY OR COLLEAGUE

<br/>

### First of all, let's create a confortable place (folder) on our computer where we will do the work and put everything we need for this tutorial:

<br/>

⚠️ TEMPLATE COMMANDS ⌨️

Move to your home directory with the "cd" command
```
cd
```
Then create the directory in which we will work using the "mkdir" command

```
mkdir META_TUTO
```
Then verify that you have really created the directory with the "ls" command

```
ls
```

Is everything okay? Do you see the META_TUTO directory now?

If yes, you can go in it, again using the "cd" command

```
cd META_TUTO
```


### Let's begin by downloading the toy dataset (They are real data from the lab!). It is a compressed file (.zip) containing metagenomics reads from 3 different samples (3 Stool-derived in vitro communities).


Choose carefully where you download the files. Put it in the META_TUTO directory you have just created!

<br/>

[Click here to download the toy read dataset](TUTORIAL_BACTERIAL_ABUNDANCE_METAGENOMICS_SIC.zip)

<br/>

Once you have downloaded the files, you can decompress them (unzip them).

<br/>

If you also open your file browser, you should see this after decompression of files

<br/>

<img width="600" alt="image" src="https://user-images.githubusercontent.com/125351299/219516955-4c866de5-5391-4608-bbcf-0d2d59ff6fdb.png">

<br/>


When we do Illumina sequencing, we always have the choice to to single-end or paired-end sequencing. Single-end will generate only one reads file (R1), and paired-end will generate two reads files (R1 and R2)


What is actually found inside a FASTQ file?

You have blocks containing 4 lines for each read (can contains millions of blocks)

 
<img width="600" alt="image" src="https://user-images.githubusercontent.com/125351299/218717635-aab09855-bda8-4a62-ae45-c8faca411917.png">


What interesting (or not) information can you find in the identifier line? 


 <img width="400" alt="image" src="https://user-images.githubusercontent.com/125351299/218717730-47ee4513-e10c-4627-8c5f-54689a309d89.png">

<br/>


### STEP 2. CHECKING THE INTEGRITY OR DAMAGE OF THE READ FILES USING md5sum

⚠️ PROGRAMS TO INSTALL 💻 

:small_blue_diamond: MD5SUM

It should already be installed on Linux and MacOS. You can check if you already have it by typing the following in your terminal:

```
md5sum --help
```

If you are being told that you don't have it, we will need to install it using BREW (not discussed here, but you can ask me).

### Short explanation for this step

There is a slight possibility that the files you have received or downloaded might be damaged or corrupted. If they are, they could create errors during the following steps, or even produce false results without you noticing anything. 

That's why it is important to check file integrity, which is usually done using what we call the md5sum.

The md5sum corresponds to a unique code that was given to each file when it was first created. If there is the tiniest difference in that file after a download or transfer, the md5sum will be different. If you get two different md5sum for a same file, it means that it is certainly corrupted/damaged.

<img width="800" alt="image" src="https://user-images.githubusercontent.com/125351299/218717916-8486b174-1154-43de-9f55-8d3c844d31a8.png">

 
 
This is what is inside the MD5.txt file. It contains the files in your folder and the verification codes.

![image](https://user-images.githubusercontent.com/125351299/219517451-62b2e2c3-bceb-45a0-a012-2e7368bbf951.png)


Using the terminal, go in the directory where your files are (the files you want to verify). In this folder you will find the MD5.txt file you have to use for the verification.   

⚠️ TEMPLATE COMMANDS ⌨️

```
md5sum --check MD5.txt
```


### STEP 3. RENAMING YOUR FILES FOR MORE CONVENIENCE DURING THE WHOLE PROCEDURE

 ⚠️ PROGRAMS TO INSTALL 💻
 
 
 No special program to install for this step
 
 
 
 
 
 
 <img width="900" alt="image" src="https://user-images.githubusercontent.com/125351299/219362889-2814ca64-895c-4c97-a556-7b9ebf8fb8bc.png">

 

Working with only few samples vs lot of samples...

Loops or no loops...




⚠️ TEMPLATE COMMANDS ⌨️


FIRST, IT IS SAFER TO TEST YOUR NAME CHANGING COMMAND WITHOUT ACTUALLY CHANGING THE NAMES FOR REAL!

```
for file in *L1_1.fq.gz do echo mv "$file" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_1*.fq.gz/R1_001.fastq.gz}"; done


for file in *L1_2.fq.gz; do echo mv "$file" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_2*.fq.gz/R2_001.fastq.gz}"; done
```

NOW CHANGING NAMES FOR REAL, BE CAREFUL !

```
for file in *L1_1.fq.gz; do mv "$file" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_1*.fq.gz/R1_001.fastq.gz}"; done


for file in *L1_2.fq.gz; do mv "$file" "${f/EKDL220000599-1a-*_H23YVDSX3_L1_2*.fq.gz/R2_001.fastq.gz}"; done 
```

### STEP 4. READ TRIMMING AND QUALITY REPORTS

 ⚠️ PROGRAMS TO INSTALL 💻
 
:small_blue_diamond: FASTP (https://anaconda.org/bioconda/fastp)

```
conda install -c bioconda fastp
```

:small_blue_diamond: MULTIQC (https://anaconda.org/bioconda/multiqc)

```
conda install -c bioconda multiqc
```


Principle

 <img width="600" alt="image" src="https://user-images.githubusercontent.com/125351299/218718565-ac0497f7-8333-4fe3-b62c-4a69b26dee82.png">


 ![image](https://user-images.githubusercontent.com/125351299/218718609-35779397-1a97-4210-b2ef-a95940d62a8c.png)




⚠️ TEMPLATE COMMANDS ⌨️

```
mkdir CLEANED_READS
mkdir REPORTS_CLEANED_READS


for prefix in $(ls *.fastq.gz | sed -E 's/_R[12]_001[.]fastq.gz//' | uniq)
do
echo "performing cleaning on files :" "${prefix}_R1_001.fastq.gz" "${prefix}_R2_001.fastq.gz"
fastp -i "${prefix}_R1_001.fastq.gz" -I "${prefix}_R2_001.fastq.gz" -o CLEANED_READS/"${prefix}_FASTP_R1_001.fastq.gz"  -O CLEANED_READS/"${prefix}_FASTP_R2_001.fastq.gz" --report_title "${prefix}_fastp_report" --thread 10 -j REPORTS_CLEANED_READS/"${prefix}_fastp".json -h REPORTS_CLEANED_READS/"${prefix}_fastp".html
done

multiqc ./REPORTS_CLEANED_READS/ --ignore-symlinks --outdir ./REPORTS_CLEANED_READS --filename MULTIQC_ALL_SAMPLE_REPORT --fullnames --title MULTIQC_ALL_SAMPLE_REPORT
```





### STEP 5. FILTERING OUT UNWANTED READS (known contaminants, host reads, etc)

 ⚠️ PROGRAMS TO INSTALL 💻 
 
:small_blue_diamond: BOWTIE2


Principle :

 <img width="800" alt="image" src="https://user-images.githubusercontent.com/125351299/218718771-821e432e-0e13-400d-85c4-e40949c359bf.png">



⚠️ TEMPLATE COMMANDS ⌨️

```
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
```

### STEP 6.  SUBSAMPLING THE CLEANED READS TO (optional step, dependant on the case)


 ⚠️ PROGRAMS TO INSTALL 💻
 
:small_blue_diamond: SEQTK


⚠️ TEMPLATE COMMANDS ⌨️

```
mkdir ./SUBSAMPLED_READS

for prefix in $(ls *.fastq.gz | sed -E 's/_001[.]fastq.gz//' | uniq)
	do
	echo "Subsampling your file :" "${prefix}_001.fastq.gz"
	seqtk sample -s 10 "${prefix}_001.fastq.gz" 1000000 > ./SUBSAMPLED_READS/SUB_1MILLION_"${prefix}_001.fastq"
done
```

### STEP 7. GETTING THE BACTERIAL ABUNDANCES

 ⚠️ PROGRAMS TO INSTALL 💻

:small_blue_diamond: METAPHLAN4


 
⚠️ TEMPLATE COMMANDS ⌨️


```
mkdir METAPHLAN_ANALYSIS/

for prefix in $(ls *.fastq.gz | sed -E 's/_FASTP_R[12]_001[.]fastq.gz//' | uniq)
do
    echo "Performing metaphlan3 analysis on sample": "${prefix}_FASTP_R1_001.fastq.gz"
    metaphlan "${prefix}_FASTP_R1_001.fastq.gz" --index mpa_vJan21_CHOCOPhlAnSGB_202103 --bowtie2db /Users/julian/Bioinfo_Local/metaphlan4_DATABASES --nproc 10 --no_map -t rel_ab_w_read_stats --unclassified_estimation --input_type fastq -o METAPHLAN_ANALYSIS/"${prefix}"_metaphlan4_abundance.txt
done

#Then to merge the profiled samples to put them all together :

merge_metaphlan_tables.py METAPHLAN_ANALYSIS/*_metaphlan4_abundance.txt > METAPHLAN_ANALYSIS/MERGED_abundance_table.txt

```


Additional material

