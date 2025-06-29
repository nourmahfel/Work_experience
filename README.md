# Practical: Quality Control and Trimming

In this practical, you will learn to import, view, and check the quality of raw high-throughput sequencing data using FastQC and Trimmomatic (via Docker).

The first dataset is from an Illumina MiSeq sequencing of *enterohaemorrhagic E. coli* (EHEC), serotype O157 — a potentially fatal gastrointestinal pathogen involved in a 2011 outbreak in St. Louis, USA. The data is paired-end 2×150 bp reads. This training requires linux and docker.

## Working Directory

### Create a working directory

```bash
mkdir ~/work
cd ~/work
```
## Downloading the Data

🧪 In this practical, you will use the [European Nucleotide Archive](https://www.ebi.ac.uk/ena/browser/home) (ENA) to download real Illumina sequencing data for quality control and trimming exercises. ENA is a public database that stores nucleotide sequencing data, such as DNA and RNA sequences, submitted by researchers around the world.

It is part of the EMBL-EBI (European Bioinformatics Institute). ENA provides free access to raw sequencing reads, genome assemblies, and functional annotation. Accession numbers like SRR957824 are unique IDs that help you find specific datasets.

The raw data is available on ENA under accession number SRR957824. 

### Step 1: Download data
We will now download real sequencing data from the European Nucleotide Archive (ENA), a freely accessible public repository. This archive stores raw reads, genomes, and other sequence data submitted by researchers globally. Wget is used to fetch the data.

Each dataset has a unique accession number. In this case, we are using accession number SRR957824.

```bash
wget https://raw.githubusercontent.com/nourmahfel/work_experience/master/data/SRA/SRR957824.fastq
wget https://raw.githubusercontent.com/nourmahfel/work_experience/master/data/SRA/SRR957824_trimmed.fastq

```
---

### Step 2: Check file size
Make sure both FASTQ files downloaded correctly and are present by running the command below.

```bash
ls -l
```
Command explanation:

ls — list. This shows the files in the current folder.

-l — long format, showing file size, date, and permissions.

-h — human-readable. This displays file sizes in KB/MB instead of raw bytes.

You should see two .fastq files.


There are ~ 80 000 paired-end reads taken randomly from the original data

One last thing before we get to the quality control: those files are writeable. By default, UNIX makes things writeable by the file owner. This poses an issue with creating typos or errors in raw data. We fix that before going further

```bash
chmod u-w *
```
Command explanation:

chmod — change mode. This edits file permissions.

u-w — removes write permission from the user (you).

star — means “apply this to all files in the folder.”

---

### Step 3: Preview a FASTQ file

```bash
zless SRR957824_1.fastq.gz
```

zless — lets you view compressed .gz text files without unzipping.

**Tip:** Use the spacebar to scroll and `q` to exit.

---

The fastq format is a text-based format that represents nucleotide sequences but also contains the corresponding quality of each nucleotide. It is the standard for storing the output of high-throughput sequencing instruments such as the Illumina machines.

A fastq file uses four lines per sequence:

Line 1 begins with a '@' character and is followed by a sequence identifier and an optional description (like a FASTA title line).

Line 2 is the raw sequence of letters.

Line 3 begins with a '+' character and is optionally followed by the same sequence identifier (and any description) again.

Line 4 encodes the quality values for the sequence in Line 2, and must contain the same number of symbols as letters in the sequence.

You can read more on the FASTQ format [here](https://www.hadriengourle.com/tutorials/file_formats/).

---

## FastQC (Pre-Trimming Quality Check)

To check the quality of the sequence data, we will use a tool called FastQC.

FastQC has a graphical interface and can be downloaded and run on a Windows or Linux computer without installation. It is available [here](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/).

However, FastQC is also available as a command-line utility in Docker. Docker makes things simple and consistent. Instead of installing software like FastQC or Trimmomatic yourself, Docker lets you download small packages (called containers) that already have everything set up. You can then run them directly on your data using simple commands. Docker makes it easy to run bioinformatics tools without worrying about installation or compatibility.

Think of Docker like a shipping container for software. Just like shipping containers hold goods and can be transported anywhere, Docker containers hold software and can run anywhere: on your laptop, a lab server, or the cloud without needing to be unpacked or reinstalled. Everything the program needs is bundled inside. 

### Step 1: Pull the FastQC Docker image
```bash
docker pull biocontainers/fastqc:v0.11.9_cv8
```
---
### Step 2: Run FastQC in Docker
```bash
docker run --rm -v "$PWD":/data biocontainers/fastqc:v0.11.9_cv8 fastqc /data/SRR957824.fastq /data/SRR957824_trimmed.fastq
```
This runs FastQC using Docker. It will generate .html and .zip results in your current directory.

---
### Step 3: List output files
```bash
ls *fastqc*
```
For each file, FastQC has produced both a .zip archive containing all the plots and a HTML report.
Open the HTML files with your favourite web browser.

---


**Question:** What should you pay attention to in the FastQC report?

**Question:** Which file is of better quality?

**Question:** What improvements are visible?

**Question:** What are the potential consequences of skipping quality control in a diagnostic laboratory?


Pay special attention to:
- Per-base sequence quality
- Sequence length distribution
- Adapter content
  
Explanations for the various quality modules can be found [here](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/). Also, have a look at examples of a good and a bad illumina read set for comparison.
You will note that the reads in your uploaded dataset have fairly poor quality (<20) towards the end. There are also outlier reads that have very poor quality for most of the second half of the reads.

---


## Summary
- You downloaded and inspected paired-end Illumina reads.
- Used Docker-based FastQC to assess raw data.
- Compared your output results.

This workflow ensures data quality and prepares reads for reliable downstream analysis.

## 📁 Folder Contents

### `answers/`
This folder contains:
- The **completed workbook answers** to the practical questions.

### `results/`
- The **FastQC output reports** (`.html`) generated before and after trimming.

### `data/SRA/`
This folder includes:
- The **fastq file** used to run the exercise
- The **trimmed fastq file** used to run the exercise.

### `fastqc_example_res/`

This folder provides:

- Example FastQC results from previously analysed datasets.
- Includes one example of high-quality and one of low-quality sequencing data to help users learn how to interpret FastQC reports.

---

