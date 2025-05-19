# Practical: Quality Control and Trimming (with Docker and Trimmomatic)

In this practical you will learn to import, view, and check the quality of raw high-throughput sequencing data using FastQC and Trimmomatic (via Docker).

The first dataset is from an Illumina MiSeq sequencing of *enterohaemorrhagic E. coli* (EHEC), serotype O157 — a potentially fatal gastrointestinal pathogen involved in a 2011 outbreak in St. Louis, USA. The data is paired-end 2×150 bp reads.

---

## Working Directory

### Create a working directory

```bash
mkdir ~/work
cd ~/work
```
## Downloading the Data

The raw data is available on ENA under accession number SRR957824. 

### Step 1: Download data

```bash
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR957/SRR957824/SRR957824_1.fastq.gz
wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR957/SRR957824/SRR957824_2.fastq.gz
```

### Step 2: Check file size and lock them

```bash
ls -lh
```

**Question:** Where does the filename come from?



**Question:** Why are there `_1` and `_2` in the file names?


---

### Step 3: Preview a FASTQ file
```bash
zless SRR957824_1.fastq.gz
```

**Tip:** Use the spacebar to scroll and `q` to exit.

---

## FastQC and Trimmomatic (Pre-Trimming Quality Check)

Please note that this exercise uses Docker to run the required bioinformatics tools because it makes things simple and consistent. Instead of installing software like FastQC or Trimmomatic yourself, Docker lets you download small packages (called containers) that already have everything set up. You can then run them directly on your data using simple commands. Docker makes it easy to run bioinformatics tools without worrying about installation or compatibility.

Think of Docker like a shipping container for software. Just like shipping containers hold goods and can be transported anywhere, Docker containers hold software and can run anywhere: on your laptop, a lab server, or the cloud without needing to be unpacked or reinstalled. Everything the program needs is bundled inside. 

### Step 1: Pull the FastQC Docker image
```bash
docker pull biocontainers/fastqc:v0.11.9_cv8
```

### Step 2: Run FastQC in Docker
```bash
docker run --rm -v "$PWD":/data biocontainers/fastqc:v0.11.9_cv8 fastqc /data/SRR957824_1.fastq.gz /data/SRR957824_2.fastq.gz
```

### Step 3: List output files
```bash
ls *fastqc*
```

Open `SRR957824_1_fastqc.html` and `SRR957824_2_fastqc.html` in a browser.

**Question:** What should you pay attention to in the FastQC report?



**Question:** Which file is of better quality?


Pay special attention to:
- Per-base sequence quality
- Sequence length distribution
- Adapter content

---

## Adapter Trimming with Trimmomatic

### Step 1: Download adapter file
```bash
curl -O -J -L https://osf.io/v24pt/download -o adapters.fa
```

### Step 2: Pull Trimmomatic Docker image
```bash
docker pull quay.io/biocontainers/trimmomatic:0.39--hdfd78af_2
```

### Step 3: Run Trimmomatic (paired-end)
```bash
docker run --rm -v "$PWD":/data quay.io/biocontainers/trimmomatic:0.39--hdfd78af_2 trimmomatic PE -phred33   /data/SRR957824_1.fastq.gz /data/SRR957824_2.fastq.gz   /data/trimmed_1.fastq /data/trimmed_1_unpaired.fastq   /data/trimmed_2.fastq /data/trimmed_2_unpaired.fastq   ILLUMINACLIP:/data/adapters.fasta:2:30:10   LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

**Question:** What adapters were used and how does Trimmomatic identify them?



**Question:** What are the unpaired FASTQ files, and why are they generated?



---

## FastQC (Post-Trimming Quality Check)

### Step 1: Run FastQC again
```bash
docker run --rm -v "$PWD":/data biocontainers/fastqc:v0.11.9_cv8 fastqc /data/trimmed_1.fastq /data/trimmed_2.fastq
```

### Step 2: List outputs
```bash
ls *trimmed*_fastqc.html
```

Open both `.html` files in your browser.

**Question:** What improvements are visible after trimming?



**Question:** How did trimming affect per-base quality and read lengths?


---

**Question:** Question: Which FastQC modules showed the most improvement after trimming?


**Question:** Did the adapter content change after trimming? How can you tell?



**Question:** How does sequence length distribution look after trimming compared to before?



**Question:** Would you expect every dataset to need trimming? Why or why not?



**Question:** What are potential consequences of skipping quality control and trimming in a real project?



---

## Summary
- You downloaded and inspected paired-end Illumina reads.
- Used Docker-based FastQC to assess raw data.
- Trimmed adapters and low-quality regions using Trimmomatic.
- Verified improvements using FastQC.

This workflow ensures data quality and prepares reads for reliable downstream analysis.

