McSplicer
=========
**McSplicer** is a probabilistic model for estimating splice site usages, rather than modeling an individual outcome of a splicing process such as exon skipping. We assume that potential 5' and 3' splice sites are given. This information can be obtained from annotation databases or estimated from RNA-seq reads by running existing assemblers.  The potential splice sites partition a gene into a sequence of segments. We introduce a sequence of hidden variables, each of which indicates whether a corresponding segment is part of a transcript. We model the splicing process by assuming that this sequence of hidden variables follows an inhomogeneous Markov chain, hence the name **M**arkov **c**hain **Splicer**. The parameters in the model are interpreted as splice site usages. We use **EM algorithm** to maximize the likelihood of these parameters. Using splice site estimates, one can describe the splicing processes, and estimate the probabilities of various local splicing events.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

   ![McSplicer](https://github.com/canzarlab/McSplicer/blob/master/Figures/McSplicer_summary.png) 



Using this software
-------------------

## Installation<a name="installation"></a>

```shell
git clone https://github.com/canzarlab/McSplicer.git
```

You can execute the script:

```shell
python ./python_code/McSplicer.py --help
```


### Dependencies<a name="dependencies"></a>

McSplicer was implemnted and tested on python 2.7, and requires only few standard packages:
- numpy>=1.13.1
- pandas>=0.20.3

## Usage <a name="usage"></a>

Initially, you need as inputs:
* The annotation in GTF format, which can be obtained from available reference annotation, e.g., Homo sapiens genome assembly GRCh37 (hg19), or estimated from RNA-seq reads using existing assemblers such as StringTie. For example the GTF files in `./examples/gtf/` were generated by running StringTie with a genome-guided mode.

* Aligned, sorted and indexed RNA-seq reads in SAMTools BAM file. For example the BAM files in `./examples/bam/` were aligned using STAR, then sorted and indexed using SAMTools.

You can run McSplicer easily in 3 steps:

1. Run exonRefine to refine the set of exons into non-overlapping, contiguous subexonic regions, i.e., signatures. For example, in the figure shown above we have 4 distinct signatures numbered in the grey rectangles {1, 2, 3, 4}.

```shell
./bin/exonRefine  <annotation.gtf> --prefix OUTPUT_PREFIX
```
 
2. Run SigCount on the refined annotation generated in the previous step together with the input BAM file to generate signature counts, i.e., the number of reads mapped to each of the signatures or their junctions. For example, in the figure above we have have 16 reads mapped to the first signuture and 4 reads mapped to the junction 3-4.


```shell
 ./bin/sigcount <alignments.bam> <annotation_refined.gtf> <outfile-prefix>
```
 
		
3. Run McSplucer to get splice site usage estimates. 
```shell
python ./python_code/McSplicer.py \
		--gtf REFINED_GTF \
		--count_file SE_SIGNATURE_COUNT \
		--gene_id GENE_ID\
		--out_dir OUTPUT_DIRECTORY\
		--bootstraps NUM_OF_BOOSTRAPS\
		--read_len READ_LENGTH\
		--prefix OUT_FILE_PREFIX

  ```
  
  Execute McSplicer script with `--help` option for a complete list of options.  

Sample data and usage examples can be found in `./examples` subfolder.

### Output: ###

The output csv file contains the bootstrap step, splice site index, gene strand, chromosome, splice site genome position, and McSplicer splice site usage estimate.
 
 If you choose to run McSplicer with ```--bootstraps n```, step 0 in the output file corresponds to the estimates based on input count data, and the following n steps correspond to the estimates of bootstrap count data.
 
 Splice site index column represents the index of 3' start or 5' end splice sites as they appear in a gene according to their chronological order, e.g., s<sub>0</sub>, s<sub>1</sub>,..., e<sub>0</sub>, e<sub>1</sub>,.. see the figure above for illustration.
 


McSplicer developers:
----------------------------
* Israa Alqassem (alqassem.isra@gmail.com)
* Yash Kumar Sonthalia (yks01247@gmail.com)


&copy; 2020 McSplicer





