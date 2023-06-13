# KRLA

## placeholder
# Organization and Workflow for *Krascheninnikovia lanata* GBS 
Organizational notes and code for two sequencing sets:
- rangewide sampling for landscape genomic analyses

# Range-wide landscape genomics: sample organization and GBS workflow 

## Sample organization
- Full information on DNAs for each individual sampled across natural distribution can be found in `XXXXXXXXXX`. This file also has the updated plate maps with specified IDs.

- **NOTE** DNA was extracted in December 2022 at AG Biotech. Plates in lab freezer need to be tranported to -80.

## GBS workflow



## Notes on library preparation

**NEED TO DO**: anneal more MSE adaptors, order iproof (have martinson order a tube), order ladder

### 12/19-12/22: R/L and PCR for plates 1-6. Master mix in `KRLA_RFseq_mastermixcockatils.xlsx`.


## Data analysis: contaminant cleaning, barcode parsing, data storage, directory organization, and initial analyses.

We generated 1 lane of S2 chemistry NovaSeq data at UTGSAF in March of 2023. 

## This file contains code and notes for
1) cleaning contaminants using tapioca
2) parsing barcodes
3) splitting fastqs 

4) de novo assembly
5) reference based assembly
6) calling variants
7) filtering
8) entropy for genotype probabilities.

## 1. Cleaning contaminants

Being executed on ponderosa using tapioca pipeline. Commands in two bash scripts (cleaning_bash_CADE.sh and cleaning_bash_SEGI.sh), executed as below (6/9/23). This was for one S2 NovaSeq lanes generated in late December 2022.

Decompress fastq file:

    $ gunzip KRLA_S1_L001_R1_001.fastq.gz

Number of reads **before** cleaning:

    $ nohup grep -c "^@" KRLA_S1_L001_R1_001.fastq > KRLA_number_of_rawreads.txt &
    ## raw reads: 

To run cleaning_bash* tapioca wrapper, exit conda environment, load modules, and run bash scripts.

    $ module load fqutils/0.4.1
    $ module load bowtie2/2.2.5
    
    $ bash cleaning_bash_KRLA.sh &

# DONE TO HERE
After .clean.fastq has been produced, rm raw data:

    $ rm -rf KRLA_S1_L001_R1_001.fastq &
   
# DONE TO HERE ********

Raw data will stay stored in: /archive/parchman_lab/rawdata_to_backup/FRLA/

Number of reads **after** cleaning:

    $ nohup grep -c "^@" KRLA.clean.fastq > FRLA1_clean_reads.txt &
    # 

####################################################################################
## 2. Barcode parsing:
####################################################################################

Be sure to deactivate conda environment before running the below steps. Barcode keyfiles are `/working/parchman/KRLA/KRLA_barcode_key.csv` 

Parsing FRLA1 library:

    $ nohup perl parse_barcodes768.pl KRLA_barcode_key.csv KRLA.clean.fastq A00 &>/dev/null &


Parsing FRLA2 library:

    $ nohup perl parse_barcodes768.pl FRLA2_barcode_key.csv FRLA2.clean.fastq A00 &>/dev/null &



`NOTE`: the A00 object is the code that identifies the sequencer (first three characters after the @ in the fastq identifier).

    $ less parsereport_FRLA1.clean.fastq
    Good mids count: 1470709510
    Bad mids count: 58172533

    $ less parsereport_FRLA2.clean.fastq
    



####################################################################################
## 3. splitting fastqs
####################################################################################

For FRLA, doing this in `/working/parchman/FRLA/splitfastqs_FRLA*`

Make ids file

    $ cut -f 3 -d "," FRLA1_barcode_key.csv | grep "_" > FRLA1_ids_noheader.txt

    $ cut -f 3 -d "," FRLA2_barcode_key.csv | grep "_" > FRLA2_ids_noheader.txt


Split fastqs by individual

    $ nohup perl splitFastq_universal_regex.pl FRLA1_ids_noheader.txt parsed_FRLA1.clean.fastq &>/dev/null &

    $ nohup perl splitFastq_universal_regex.pl FRLA2_ids_noheader.txt parsed_FRLA2.clean.fastq &>/dev/null &

# DONE TO HERE &&&&&&&&&&


Zip the parsed*fastq files for now, but delete once patterns and qc are verified.

### Moving fastqs to project specific directories

Fastqs by species are located on ponderosa in:

FRLA1:
`/working/parchman/FRLA`

FRLA2:
`/working/parchman/FRLA`
