# MAPPIN
MAPPIN (Multiple Alignment for Protein Protein Interactions Networks) a global many-to-many alignment of multiple PPINs from different species.

Written By: Warith Eddine DJEDDI (waritheddine@yahoo.fr)
            Sadok BEN YAHIA (sadok.benyahia@fst.rnu.tn)
            Engelbert MEPHU NGUIFO (mephu@isima.fr)

This README describes the usage of the command line interface of MAPPIN. It is worth mentioning that our approach is based on NetCoffee.

The executable MAPPIN is compiled for Linux x86_64 platform.

The program MAPPIN finds a global alignment of multiple input
networks. Given multiple networks with N1, N2,..., Nk nodes each,
it returns a matching between the input networks, each match corresponding to
best-matching nodes from the multiple networks.

To understand how to use the algorithm, let's start with an example of
pairwise alignment of two input networks. The multiple case is similar.


(1) Suppose the species are named 'A' and 'B'

(2) Create the graph files and results from the BLAST runs (between the
    nodes) in a single directory. You'll need 5 files:

  (2.1) Network files:
     You'll need A.net and B.net , tab-separated files
     where each line contains an interaction. For example, the first 5 lines of
     A.net are:

     ====== BEGIN ========
     INTERACTOR_A INTERACTOR_B
     a0 a1
     a0 a2
     a0 a3
     a0 a4
     ====== END ========

      Columns are separated by tabs. The first line is a header line of the
     form as shown above. All other lines describe an interaction, one per
     line.

     There may be a third column which contains edge weights (0 < wt <= 1) 
     and in that case the header line should've a third column titled Weight_VAL.

  (2.2) Results of BLAST runs.
     You'll need to perform an the all-against-all run
     of BLAST between all the nodes of the two networks. You should store the results in 3 files:

        A-B.evals,  A-A.evals, B-B.evals

     The files contain, as their names indicate, the results of BLAST runs
     between species A & B, A & A, and B & B, respectively. IMPORTANT: for
     files containing Blast scores between two species, the filename should
     have the species names in lexicographic order, i.e., A-B.evals is
     expected, not B-A.evals.

     The first 5 lines of the A-B.evals file are:

     ====== BEGIN ========
     a0      b0      1
     a1      b1      1
     a2      b2      1
     a3      b3      1
     a4      b4      1
     ====== END ========

     Each line is of the form:
     <id1>  <id2>   <Bit-Score>



  (2.3)  Gene ontology (GO) File
 
     IMPORTANT: Download gene ontology file from the Website "http://www.geneontology.org/" and uncompress it 
     under the "MAPPIN\data" folder.


  (2.4) GO annotation file which contains GO annotations for proteins in the input networks. The format of this GO annotation file is compliant with the GO consortium.
     
    A) File: goa_uniprot_gcrp.gaf → This set contains all GO annotations for canonical accessions
      from the UniProt reference proteomes for all species, which provide one protein per gene.
      
    B) Files: goa_<species>.gaf for each species → This set contains all GO annotations for canonical accessions 
      from the UniProt reference proteome for the species, which provides one protein per gene. 
      

 IMPORTANT: Download gene annotation files for each species from the Uniprot Website "http://www.ebi.ac.uk/GOA/downloads" 
 and uncompress them under the "MAPPIN\data\GOA" folder.

(3) Create a file that specifies the file locations, species names etc.
   In the folder config/, the file "policy.input".

   For example, suppose we have three PPI networks:a.net, b.net, c.net, GO association file for the 3 species: a.gaf, b.gaf, c.gaf,
   six balst evalue (or bitscore) files: a-a.cf, a-b.cf,...,c-c.cf, then the policy file should looks like:

          --------------BEGIN OF POLICY--------------
                    PARAMETER VALUE
                    network   dataset/a.net
                    network   dataset/b.net
                    network   dataset/c.net
                    goafile   data/goa/a.gaf
                    goafile   data/goa/b.gaf
                    goafile   data/goa/c.gaf
                    efile     dataset/a-a.cf
                    efile     dataset/a-b.cf
                    efile     dataset/a-c.cf
                    efile     dataset/b-b.cf
                    efile     dataset/b-c.cf
                    efile     dataset/c-c.cf
                    scorefile     dataset/score_composit.model
                    alignmentfile ./result/alignment_mappin.data
                    logfile       ./result/measure_time.txt
          --------------END OF POLICY--------------

 

(4) Call the code. Here are samples:

   ./mappin -alignment -alpha 0.3 -nmax 1000 -temp 50 -thr 0.3 -numspecies 3 -numthreads 8 -alignmentfile ./result/alignment_mappin.data -resultfolder ./result/

   The options are as follows (you can also use the "-h" or "--help" flag):

     Usage:
     ./mappin -version|-alignment [--help|-h|-help] [-alignmentfile str]
       [-alpha num] [-bscore] [-edgefactor num] [-nmax int] [-numspecies int]
       [-numthreads int] [-resultfolder str] [-temp int] [-thr num]
     Where:
      --help|-h|-help
         Print a short help message
      -alignment
         Execute the alignment algorithm.
      -alignmentfile str
         The filename of alignment of protein-protein interaction networks
      -alpha num
         Prameter controlling how much biological score contributes to the alignment score. Default is 0.3.
      -bscore
         Define bitscore as the similarity for the edges.
      -edgefactor num
         The factor of the power law normalization. Default is 0.1.
      -nmax int
         The parameter N for Simulated Annealing algorithm.
      -numspecies int
         Number of the species used in the aligning process. Default is 3.
      -numthreads int
         Specifies the number of threads used to run MAPPIN. 
         The recommended number of threads is the number of cores available in the computer.
      -resultfolder str
         The folder which was used to store the alignment results.
      -temp int
         The number of iteration for Simulated Annealing algorithm.
      -thr num
         The threshold for the alignment of protein-protein interaction networks.
      -version
         Show the version number of MAPPIN.

(5)  The output

      The main files are :
       (5.1) The output of the alignment is located in the file "alignment_mappin.data". Each protein is represented 
       by a string (separated by a tab), and each interaction is on a single line.
       (5.2) The affectation of the gene annotations for each protein belonging to the N input networks is located 
       in the file "affectProtToGene.txt".
       (5.3) The number of unkown functional protein is located in the file "alignUnknown.result".
       (5.4) The required time to align the input networks is located in the file "measure_time.txt".
       (5.5) The sequence, topological and functional similarities for each pair of compared protein 
       is located in the file "scoreRecords.txt".
       (5.6) The informations : Alignment edges conserved for each species, the number of alignment nodes, 
       the Alpha parameter, nmax and the Alignment score, are located in the file "alignment_statistics.data".
       (5.7) The number of unknown alignment records, and the number of proteins annotated with MF,BP and CC in alginment graph,
       are located in the file "statistics.result".

(6)  EXAMPLE
       (6.1) Download MAPPIN freely available at Github website: https://github.com/waritheddine/MAPPIN

       (6.2) Run MAPPIN on our test dataset with command:
       ./mappin -alignment -alpha 0.3 -nmax 1000 -temp 50 -thr 0.3 -numspecies 3 -numthreads 8 -alignmentfile ./result/alignment_mappin.data -resultfolder ./result/

       (6.3)Then you can find the all the involved output files in ./result/ . There are many other functions which you can see with "-help" option.
       (6.4) We note that checking the format of the files in the data folder after reading the execution instructions above might be quite helpful.
       (6.5) For this example, download the gene annotation file (goa_fly.gaf.gz, goa_worm.gaf.gz and goa_yeast.gaf.gz) for the three species 
       (Fly, Worm and Yeast) from the Uniprot Website "http://www.ebi.ac.uk/GOA/downloads" and uncompress them under the "MAPPIN\data\GOA" folder. In addition, dowload the compress file "goa_uniprot_gcrp.gaf.tar.gz" from the same Url, and also uncompress it under the "MAPPIN\data\GOA" folder.
       Finally, download gene ontology file from the Website "http://www.geneontology.org/" and uncompress it under the "MAPPIN\data" folder.
