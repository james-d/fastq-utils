# fastq-utils
Utilities for general batch-processing of fastq files.

These utilites are general shell scripts for processing multiple fastq files. 
They are typically linked to specific versions of open-source bioinformatics software,
in order to enhance reproducibility. Updates to this repository include updates to 
the scripts, and updates to the software versions on which they depend.

All scripts support provide help via a `-h` or, equivalently, `--help` option. 

---

copy_fastqs.sh
==============

Simple utility to copy fastq files from paired-end runs from one or more 
Illumina-style output folders to a target folder. This is
helpful in the case where fastq files from multiple runs or projects will be analyzed as
part of the same analysis. 

Assumptions:
------------

1. Each source folder contains a collection of sample folders whose names are of the form `Sample_samp-name`, where `samp-name` is the sample name.
2. All sample names are unique across all source folders.
3. Each sample folder contains matching pairs of "read 1" and "read 2" fastq.gz files, with the "read 1" file names containing the string `_R1` and the "read 2" files being identical except that `_R1` is replaced by `_R2`. Note this means that the sample name must not contain the string `_R1` or `_R2`, or begin with the string `R1` or `R2`.
 
Requirements:
-------------

None.

Syntax:
-------

    copy_fastqs.sh destination_dir project_dir1 [project_dir2 [project_dir3 ...]]

Notes:
------

Each copy is performed in the background. For a very large number of files, you must be able to open a sufficient number of file handles.

---

generate_qcs.sh
===============

Utility to generate quality control files for multiple fastq files, using [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) version 0.11.3.

Assumptions:
------------

`source_dir` contains a collection of `fastq.gz` files.

Requirements:
-------------

1. [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/) version 0.11.5.
2. [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8.0_102


Syntax:
-------

    generate_qcs.sh [options] source_dir output_dir
    
Notes:
------

Runs as a single process. Use the `-t` (`--threads`) option to specify the number of files that can be simultaneously processed.


    
---

sort_and_index_bams.sh
======================

Sorts and indexes a collection of bam files, using [samtools](http://www.htslib.org/) version 1.3.1.


Assumptions:
------------

`source_dir` contains a collection of bam files.

Requirements:
-------------

1. [samtools](http://www.htslib.org/) version 1.3.1

Syntax:
-------

    sort_and_index_bams.sh [options] source_dir dest_dir
    
---

mark_duplicates.sh
==================

Marks duplicates in a sorted, indexed bam file, using [picard](http://broadinstitute.github.io/picard/) version 2.6.0, [Java development kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8.0_102. Resulting bam files are re-indexed using [samtools](http://www.htslib.org/) version 1.3.1.

Assumptions:
------------

1. `source_dir` contains a collection of *sorted* bam files with read group information, with each bam file being indexed with a matching bai file.

Requirements:
-------------

1. [picard](http://broadinstitute.github.io/picard/) version 2.6.0 
2. [Java development kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8.0_102.
3. [samtools](http://www.htslib.org/) version 1.3.1.

Syntax:
-------

    mark_duplicates.sh [options] source_dir dest_dir

trim.sh
=======

Trims paired files *_R1.fastq.gz and *_R2.fastq.gz in `source_dir`, outputting trimmed files with the same name as the original files to `dest_dir` and unpaired reads after trimming to files with the same name in `unpaired_dir`. Uses [trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) version 0.36 to perform trimming. Optionally performs adapter trimming using the paired-end adapter protocol, and the file specified by `adapterFile`. Adapter trimming is omitted if this option is omitted. Performs leading and trailing base trimming with minimum quality score of 3, sliding window trimming with window size of 4 and required quality of 15, and removes reads with a trimmed length of less than 25.

Requirements:
-------------

1. [trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) version 0.36
2. [Java development kit](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8.0_102.
 

Syntax:
-------

    trim.sh [options] source_dir dest_dir unpaired_dir
    
