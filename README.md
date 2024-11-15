### NEW in this forked repo
Added -chr option for calculating TAD-fusion score. Now you can use it with any genome you like! Just specify the number of chromosomes with the new option.

### What is TAD-fusion score?
TAD-fusion score is a score to quantify deletions based on their potential disruption of the 3D genome structure. More specifically, TAD-fusion score is defined as the expected number of additional genomic interactions created as a result of the deletion.

### How to calculate the TAD-fusion scores of a deletion set?

##### Option 1: From 5kb Hi-C data of GM12878 of [Rao et al](https://www.cell.com/abstract/S0092-8674(14)01497-4)
  
1. Compile the TAD-fusion score tool by running the script
   ```
   ./compile_cal_tad_fusion_score.sh
   ```
2. Prepare the input deletion file (3-column format as the [sample file](./Data/disease_del.dat), with hg19 as the reference)
3. Run the tool with default parameters as 
   ```
   ./../src/cal_tad_fusion_score -md ../Model/GM_Rao_5kb -f ../Data/disease_del.dat -mnl 10000 -mxl 5000000 -w 100 -d 0.06 -o Output/disease_del_TAD_fusion_score.dat
   ```
4. The output file is "Output/disease_del_TAD_fusion_score.dat", the last column is the TAD-fusion score
5. Sample scripts are in the folder [Examples](./Examples), here are options for calculating the TAD-fusion score
  
       -md       Model directory, model files must be renamed as chr1.model, chr2.model, ..., chrX.model 
       -f        The file that stores deletions that we need to calculate the TAD-fusion score, the file format has three columns (e.g. one row is "chr2    221278232       223014332")
       -mnl      The minimum length (a number of base pairs), any deletion that is shorter than this threshold will be skipped
       -mxl      The maximum length, any deletion that is longer than this threshold will be skipped
       -w        The window length (a number of bins) around the deletion to calculate the TAD-fusion score
       -d        The delta value threshold to consider if a bin pair is interacted or not.
       -o        The output file, the file format has four columns where the last one is the TAD-fusion score  
     NEW IN THIS FORK
       -chr      The maximum number of chromosomes in the model directory. The default number is 23.
   
##### Option 2: From a new Hi-C dataset

1. Fit the model with Hi-C data

     * a. Install CPLEX
     * b. Set variables CPLEX_INCLUDE and CPLEX_LIB (in file [make_fit_hic_model](./src/make_fit_hic_model)) to the directory where CPLEX is installed
     * c. Compile the source by running the script
          ```
          ./compile_fit_hic_model.sh
          ```
     * d. If the compilation is successful, an executable file "fit_hic_model" will be generated in the folder "src"
     * e. Options for fitting the model

            -fn       Data file path
            -ff       Data file format ("full_matrix_format" of Schmitt et al. data or "sparse_matrix_format" of Rao et al. data)
            -res      Hi-C matrix bin resolution (e.g. 40kb, 10kb, 5kb)
            -mn       Minimum distance (by a number of bins), any bin pair that the distance is shorter than this threshold will not be considered for fitting the model
            -mx       Maximum distance, any bin pair that the distance is longer than this threshold will not be considered for fitting the model
            -method   Method for fitting ("full" to fit the model from the whole Hi-C data at one time or "segmentation" to partition the matrix into segments and then fit the model for each segment)
            -sg       Length (i.e. a number of bins) of a segment (in the case the method is set to "segmentation")
            -mso      The minimum overlap (i.e. a number of bins) between two segments (in the case the method is set to "segmentation")
            -zero     A constant to replace the zero value to take the log
            -of       The output model file (the file format has 4 columns where alpha, beta, and the insulation are 1st, 3rd, 4th column respectively)
        
     * f. Example: The script file "fitting.sh" (in folder "Examples") is to fit the model of chr22 of GM12878 from Schmitt et al. data
        * Run the script by
          ```
          cd Examples
          ./fitting.sh
          ```
        * The output model file is "GM12878.40kb.chr22.model" in folder "Examples/Output".
        * In the model file, 1st, 2nd and 4th columns are alpha, beta, and the insulator respectively.
     * g. For your convenience, we also provide models (in the folder "Model") that we fitted for GM12878 from Rao et al. data at 5kb resolution.

2. Run TAD-fusion score tool (with the new model) to get the TAD-fusion score (as the section above)

### Support

If you have any questions about TAD-fusion score, please contact Linh Huynh (huynh@ucdavis.edu) or Fereydoun Hormozdiari (fhormozd@ucdavis.edu).

### Citation

Huynh L, Hormozdiari F. [TAD-fusion score: discovery and ranking the contribution of deletions to genome structure](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1666-7). Genome Biology. 2019; 20:60.

### Licence

See the [LICENSE](./LICENSE.txt) file for license rights and limitations (BSD-2).

### Acknowledgement

This work is supported in part by the Sloan Research Fellowship number G-2017-9159 to Fereydoun Hormozdiari.
