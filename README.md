# CPTAC3 data catalog

Holds details about CPTAC3 data at GDC and results at DCC, as well as details about downloaded data

## v2.0 update

Files `discover.20191111.Catalog.dat` and `discover.20191111.Demographics.dat` are draft releases of `v2.0` release of CPTAC3 discovery.
New features include,

* Methylation array support
* Targeted sequencing support
* Added full sample type column
* Aliquot information
* Added column 10, `result_type`, and shifted remaining columns to right. This column codes for two distinct things:
    * For Methylation Array data, it is the channel (Green or Red)
    * For RNA-Seq harmonized BAMs, it is the result type, with values of genomic, chimeric, transcriptome
* AR file renamed Catalog file
* `file_summary` file renamed Catalog.Summary


## Overview 

* `CPTAC3.cases.dat`: All known cases associated with CPTAC3 project.  This is the master list
    * This also defines the cohort (discovery or confirmatory) and batch of each case.  Note that each case may be in multiple batches
* `CPTAC3.Catalog.dat`: Details about all sequence data (WGS, WXS, RNA-Seq, miRNA-Seq, Methylation Array, Targeted Sequencing) at GDC associated with all known cases
    * Note that this was previously called `CPTAC3.AR.dat`
* `CPTAC3.Demographics.dat`: Demographic information associated with all known cases
* `CPTAC3.Catalog.Summary.txt`: Summary of files available for each case on GDC. Lists counts of tumor (T), blood normal (N), and adjacent / tissue normal (A) for each of
    * WGS.hg19 - WGS data as submitted to GDC.  Assuming hg19
    * WXS.hg19 - WXS (aka WES, exome) data as submitted to GDC.  Assuming hg19
    * RNA.fq - RNA-Seq data as submitted to GDC, FASTQ format
        * R1 and R2 FASTQs are listed individually, so will typically have two of each sample type
    * miRNA.fq - miRNA-Seq data as submitted to GDC, BAM format
    * WGS.hg38 - Harmonized WGS data
    * WXS.hg38 - Harmonized WXS data
    * RNA.hg38 - Harmonized RNA-Seq data
        * Harmonization generates chimeric, genomic, and transcriptome BAM files, so each entry will have 3 of each sample type
    * miRNA.hg38 - Harmonized miRNA-Seq data
    * MethArray - Methylation Array data
* `./BamMap` - has details about GDC data downloaded to Ding Lab
    * `*.BamMap.dat`: "BamMap" files for various systems indicating locations of downloaded hg19, hg38, and FASTQ sequence data
    * `*.BamMap-summary.txt` - summary of files available on a given system as well as GDC.
        * For given system (e.g., katmai), format is similar to CPTAC3.file-summary.txt, except that upper-case symbol indicates presence on given system
          and lower-case symbol indicates that that sample is in GDC but not on system
* `./DCC_Analysis_Summary` - has details about analyses uploaded to DCC


## Details

### Specific changes associated with v2.0

#### CPTAC3.Catalog.dat

* Added experimental_strategies "MethArray" and "Targeted Sequencing"
* column "sample_type" renamed "short_sample_name", contents unchanged
* Column "samples" replaced with "aliquot", and reports aliquot submitter ID associated with each sample
* New column 13 : "sample_type".  Reports verbatim GDC sample_type associated with sample
* Added column 10, `result_type`, and shifted remaining columns to right. This column codes for two distinct things:
    * For Methylation Array data, it is the channel (Green or Red)
    * For RNA-Seq harmonized BAMs, it is the result type, with values of genomic, chimeric, transcriptome

### Cases file

Comprehensive list of cases along with their disease, cohort, and batch information.
* Current cases list consists of 3696 cases and their disease. This information is as obtained from file `Batches1through9_samples_attribute_tumorcode_added.xlsx`.  
* Cohort is an ad hoc column which tries to categorize cases according to Discovery or Confirmatory cohort, per year of contract.
* Batch column indicates the year and batch(es) in which each case was processed.  Y1 and Y2 correspond to Year 1 and 2, respectively.
  Note that a given case may belong to several different batches, since not all data for a given case was available at a given time.
  Such batches are listed as comma-separated names.  In the future batch information should be indicated in a differnet file.

### Catalog file

List of all WGS, WXS, RNA-Seq, miRNA-Seq, Targeted Sequencing, Methylation Array data available at GDC.  Obtained with 
scripts in [CPTAC3.case.discover](https://github.com/ding-lab/CPTAC3.case.discover)

Note that submitted reads are listed as having reference hg19 for WXS and WGS and hg38 for miRNA-Seq.  This appears to be empirically
true, but subject to change.

### DCC Analysis Summary

Files here track analyses uploaded to DCC, with one file per analysis pipeline.  DCC Analysis Summary files have
the following columns:
```
 1. case
 2. disease
 3. pipeline_name
 4. pipeline_version
 5. timestamp
 6. DCC_path
 7. filesize
 8. file_format
 9. md5sum
```

Additional columns are specific to individual pipelines and will typically indicate the input data associated with this analysis.


### Catalog Summary

Catalog summary files provide a one-line representation of data available for a given case on GDC.  Following case and disease, each column represents
a particular data type, and one-letter codes T, N, A indicate availability of tumor, blood normal, and tissue adjacent normal samples, respectively.
Repeated codes indicate repeated data files.

#### Example
```
C3L-00001   LUAD        WGS.hg19 T N A      WXS.hg19 T N A      RNA.fq TT  AA       miRNA.fq T  A       WGS.hg38 T N A      WXS.hg38 T N A      RNA.hg38 TTT  AAA       miRNA.hg38 T  A     MethArray TT  AA
```
This line indicates that LUAD case C3L-00001 has tumor, blood normal, and adjacent normal samples for WGS and WXS data as submitted (hg19);
tumor and adjacent normal RNA-Seq data (TT, AA because FASTQ data comes in pairs); and tumor and adjacent miRNA data in FASTQ format.  All
these are available as harmonized hg38 WGS and WXS, and harmonized hg38 RNA-Seq chimeric, genomic, and transcriptome BAMs are available
for tumor and adjacent normal.  Methylation array data for tumor and tissue adjacent also available (Green and Red channel for each).

### BamMap files

Contents of `./BamMap` directory track in-house data downloaded from GDC.  These change frequently and are specific to Ding Lab systems.

*TODO* add details about BamMap columns

#### BamMap summary
As an example from `MGI.BamMap-summary.txt`:
```
CCRCC	    WGS.hg19 t n a	    WXS.hg19 t n a	    RNA.fq TT  AA	    miRNA.fq t  a	    WGS.hg38 T N a	    WXS.hg38 T N A	    RNA.hg38 Ttt  Aaa
```
This indicates that all RNA-Seq FASTQ, harmonized WGS tumor and blood normal, all harmonized WXS, and genomic hg38 RNA-Seq data are available at MGI.
Lower case letters indicate which data are available at GDC but not at MGI. 

*NOTE* BamMap summary files are not updated regularly and are considered deprectated.


## Additional modifications

The file `SampleRename.dat` contains aliquot ID and sample name suffix as first and second columns, respectively.
It is used to add ad hoc / custom suffixes to sample names according to either UUID or aliquot name, and is
passed as `-s SUFFIX_LIST` to [`src/make_catalog.sh`](https://github.com/ding-lab/CPTAC3.case.discover/blob/master/src/make_catalog.sh)

Currently, it is used to add suffixes `-bulk` and `-core` to select PDA WXS cases.

## Contact

Matthew Wyczalkowski <m.wyczalkowski@wustl.edu>
