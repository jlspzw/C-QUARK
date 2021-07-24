# C-QUARK

contact-based ab initio folding program, C-QUARK

The benchmark dataset can be downloaded at https://zhanglab.dcmb.med.umich.edu/C-QUARK

           INSTALLATION AND IMPLEMENTATION OF C-QUARK SUITE
   (Copyright 2020 by Zhang Lab, University of Michigan, All rights reserved)
                    (Version 1.0, 2020/10/10)


   #######################################################
   #                                                     #
    #  1. What is C-QUARK Suite?                           #
    #                                                    #
   #######################################################
   
   The C-QUARK Suite is a composite package of programs for C-QUARK based 
   protein structure prediction. The Suite includes the following programs:

   a) C-QUARK: A hierarchical program for protein structure prediction
   b) DeepMSA: A program for multiple sequence alignmnet generation
   c) MUSTER: A threading program for protein template identification
   d) CEthreader: A contact-based threading program for protein template identification
   e) LOMETS2: A meta-server approach consisting of multiple threading programs
   f) ResTriplet,TripletRes,ResPre,ResPLM and DeepPLM: Deep-learning-based programs for residue-residue contact prediction
   g) SPICKER: A clustering program for structure decoy selection
   h) HAAD: Quickly adding hydrogen atoms to protein heavy atom structure
   i) EDTSurf: Construct triangulated surfaces of protein molecules
   j) ModRefiner: Construct and refine atomic model from C-alpha traces
   k) NWalign: Protein sequence alignments by Needleman-Wunsch algorithm
   l) PSSpred: A program for Protein Secondary Structure PREDiction
   m) ResQ: An algorithm to estimate B-factor and residue-level error of models


   #######################################################
   #                                                     #
    #  2. How to install the C-QUARK Suite?              #
    #                                                    #
   #######################################################
   
   a) download the C-QUARK Suite 'C-QUARK-1.0.tar.bz2' from
      http://zhanglab.dcmb.med.umich.edu/C-QUARK/download
      and unpack 'C-QUARK-1.0.tar.bz2 by
      > tar -xvf C-QUARK-1.0.tar.bz2
      The root path of this package is called $pkgdir, e.g. 
      /home/yourname/C-QUARK-1.0. You should have all the programs under this 
      directory. You can install the package at any location on your computer.
   
   b) Download C-QUARK library files from
      https://zhanglab.dcmb.med.umich.edu/C-QUARK/download 
      A script 'download_lib.pl' is provided in the package for automated
      library download and update of the libraries.
      We recommend putting the library files under the path /home/yourname/ITLIB.

   c) Third-party software installation:

      While the majority of programs in the package 'C-QUARK-1.0.tar.bz2' are
      developed in the Zhang Lab herein the permission of use is released,
      there are some programs and databases (including blast, nr,
      uniclust30,uniref90 and metaclust) which were developed by third-party groups. 
      A default version of blast and nr are included in the package. It is user's obligation to obtain
      license permission from the developers for all the third-party software 
      before using them. In addition, your system needs to have Java, python2, 
      python3 (which supports pytorch 0.3.0) installed.

      To use DeepMSA, you need download uniclust30, uniref90 and metaclust from 
      http://gwdu111.gwdg.de/~compbiol/uniclust/2017_04/uniclust30_2017_04_hhsuite.tar.gz ,      
      ftp://ftp.uniprot.org/pub/databases/uniprot/uniref/uniref90/uniref90.fasta.gz ,
      and https://metaclust.mmseqs.org/2017_05/metaclust_2017_05.fasta.gz. after you unpack them,
      put the entire folder to the C-QUARK library folder, (i.e. where the folder you put your PDB, MTX, DEP folders).
      Then rename the folder uniclust30_xxx_xxx to uniclust30, uniref90_xxx to uniref90, metaclust_xxx to metaclust.
      Then use $pkgdir/contact/DeepMSA/bin/esl-sfetch to create .ssi index for uniref90 and metaclust, here 
      $pkgdir means the path where you put the C-QUARK suite package.
      For example, if the uniref90 database in uniref90 folder is named as uniref90.fasta,
      then go to uniref90 folder, run $pkgdir/contact/DeepMSA/bin/esl-sfetch --index uniref90.fasta, you will find
      a new file named as uniref90.fasta.ssi after the command done. Then do the same thing to metaclust database.
      If you use different version of uniclust30, uniref90 or metaclust, please go to $pkgdir/QUARKmod/runQUARK.pl,
      change the variables 
      $hhbdbdir = "$libdir/uniclust30";
      $jacdbdir = "$libdir/uniref90";
      $hmsdbdir = "$libdir/metaclust";

      $hhbdb    = "$libdir/uniclust30/uniclust30_2017_04";
      $jacdb    = "$libdir/uniref90/uniref90.fasta";
      $hmsdb    = "$libdir/metaclust/metaclust.fasta";

   #######################################################
   #                                                     #
    # 3. Bug report:                                     #
    #                                                    #
   #######################################################
                                                  
   Please report and post bugs and suggestions at C-QUARK message board: 
   http://zhanglab.dcmb.med.umich.edu/forum


   #######################################################
   #                                                     #
   #  4. Installation and implementation of C-QUARK   #
   #                                                     #
   #######################################################
   
4.1. Introduction of C-QUARK
   
   C-QUARK (stands for 'Contact-assisted QUARK') is a new method for ab initio protein structure prediction. 
   As the name indicates, C-QUARK is extended from QUARK but significantly outperforms the latter by the integration
   of deep-learning based contact-map predictions into the fragment assembly simulations. C-QUARK contains three 
   consecutive steps: First, multiple contact-maps are predicted by ResTriplet, DeepPLM, ResPLM, ResPRE, and TripletRes. 
   It then identifies structural templates from the PDB by multiple threading approach LOMETS2 (optional if you select the non-QEx mode),
   Second, QUARK-based replica-exchange Monte Carlo (REMC) folding simulations are performed under the guidance of a new 3-gradient 
   contact potential. Finally, structural conformations generated in the REMC simulations are submitted to SPICKER 
   for decoy selection, with final atomic models constructed and refined by ModRefiner.

4.2. How to run C-QUARK?
   
   a) Main script for running C-QUARK is $pkgdir/QUARKmod/runQUARK.pl. 
      Run it directly without arguments will output the help information.

   b) The following arguments must be set (mandatory arguments). One example is: 

      "$pkgdir/QUARKmod/runQUARK.pl -libdir /home/yourname/ITLIB -seqname example -datadir /home/yourname/C-QUARK-1.0/example"

      -libdir  means the path of the template libraries
      -seqname means the unique name of your query sequence
      -datadir means the directory which contains your sequence 

   c) Other arguments are optional whose default values have been set.
      User can reset one or more of them. One example of command line is: 

      "$pkgdir/QUARKmod/runQUARK.pl -pkgdir /home/yourname/C-QUARK-1.0 -libdir /home/yourname/ITLIB -seqname example -datadir /home/yourname/C-QUARK-1.0/example -homoflag benchmark -idcut 0.3 -java_home /usr -python2 /usr/bin/python2 -python3 /usr/bin/python3 -pipe QEC"

      -pkgdir     means the path of the C-QUARK package. default is to
                  guess by the location of runQUARK.pl script
      -java_home  means the path contains the java executable "bin/java"
                  (your system needs to have Java installed)
      -homoflag   [real, benchmark],"real" will use all templates, "benchmark"
                  will exclude homologous templates    
      -idcut      sequence identity cutoff for "benchmark" runs, default
                  value is 0.3, range is in [0,1]    
      -ntemp      number of top templates output for each threading program,
                  default is 20, range is in [1,50]    
      -nmodel     number of final models output by C-QUARK, default value
                  is 5, range is in [1,10]
      -traj       this option means to deposit the trajectory files
      -light      this option means to run C-QUARK in fast mode (each 
                  simulation runs by default 5 hours maximum)
      -hours      specify maximum hours of simulations (default=5 when -light=true)
      -outdir     where the final results should be saved (default value is set to data_dir)
      -python2    path to python 2, for example /bin/python 
      -python3    path to python 3 for contact prediction, need to support pytorch 0.3.0, for example /bin/python3
      -pipe       use which pipeline to fold the protein
                 QE;   #original QUARK ab initio simulation
                 QN;   #quark_ab_initio+init (templates from LOMETS2)
                 QP;   #quark_ab_initio+init+dist_prof (dist_prof derived from LOMETS2)
                 QT;   #quark_ab_initio+init+I-TASSER_restrains (whole-set of I-TASSER restratins)
                 QEC;  #QE+contact (C-QUARK, best for ab initio targets)   =default
                 QNC;  #QN+contact (second best for ab initio or hard TMB target, very fast)
                 QPC;  #QP+contact C-quark_ab_initio+init+dist_prof (dist_prof derived from LOMETS2)
                 QTC;  #QT+contact C-quark_ab_initio+init+I-TASSER_restrains (whole-set of I-TASSER restratins)

   NOTE:
   a) Outline of steps for running C-QUARK by 'runQUARK.pl':
      a1) standardize 'seq.fasta' to 'seq.txt' and get the sequence length
      a2) run 'deepmsa' to generate deep multiple sequence alignment
          run 'psiblast' to generate 'chk', 'out', 'pssm', 'mtx' files
          run 'PSSpred' to get 'seq.dat', 'seq.dat.ss'
          run 'solve' to get 'exp.dat'
          run 'pairmod' to get 'pair1.dat' and 'pair3.dat' (optional, if non-QEx mode selected)
      a3) run 'restriplet','tripletres','respre','resplm' and 'deepplm' to predicted contact maps
      a4) run 'LOMETS2' threading programs sequentially (optional, if non-QEx mode selected)
          run 'mkinit.pl' to generate restraints (optional, if non-QEx mode selected)
      a5) run C-QUARK simulation
      a6) run SPICKER clustering program
          run 'EMrefinement.pl' to get full-atomic models
   b) 'seq.fasta' is the query sequence file in FASTA format, which is the
      only needed input file for running C-QUARK. This file should be
      put in $datadir before running this job.
   c) C-QUARK structure assembly simulations contains multiple independent 
      runs by decided by protein type. This number can be modified if the user wants to run
      more simulations, especially for big protein without good templates.
   d) If the job has been executed partially and encounter some error, you can 
      rerun the main script without modification. It will check the existing 
      files and start from the correct position.

4.3 System requirement:

   a) x86_64 machine, Linux kernel OS, Free disk space of more than 60G.
   b) Perl and java interpreters should be installed. 
   c) Basic compress and decompress package should be installed to support: 
      tar and bunzip2.

4.4. How to cite C-QUARK and C-QUARK Suite?

   1. S. M. Mortuza, Wei Zheng, Chengxin Zhang, Yang Li, Yang Zhang. C-QUARK: Template-free protein structure 
      modeling using low-accuracy contact-map prediction. Submitted, 2020.
   2. Wei Zheng, Yang Li, Chengxin Zhang, Robin Pearce, S. M. Mortuza, Yang Zhang. Deep-learning contact-map 
      guided protein structure prediction in CASP13. Proteins: Structure, Function, and Bioinformatics, 87: 1149-1164 (2019).
   3. D. Xu and Y. Zhang. (2012) Ab initio protein structure assembly using continuous structure fragments and 
      optimized knowledge-based force field. Proteins, 2012, 80, 1715-1735. 
   4. D. Xu and Y. Zhang. (2013) Toward optimal fragment generations for ab initio protein structure assembly. 
      Proteins, 81: 229-239 . 

   ###################################################################
   #                                                                 #
   #  5. Installation and implementation of contact predictors       #
   #                                                                 #
   ###################################################################
   
5.1. Introduction of ResTriplet, TripletRes, ResPRE, ResPLM and DeepPLM
   
   TripletRes and ResTriplet are deep-learning based contact predictors 
   using three co-evolutionary features: the covariance matrix (COV) 
   proposed by DeepCov; the precision matrix (PRE) formulated by ResPRE; 
   and the coupling parameters of the inverse Potts model obtained through 
   pseudolikelihood maximization (PLM).

   ResPRE is our in-house contact-map predictor, which consists of two 
   consecutive steps of precision matrix-based feature generation and 
   deep residual neural network-based contact inference.

   ResPLM is also an in-house contact-map predictor similar to ResPRE. 
   The only difference is that ResPLM was trained using the PLM feature. 
   
   DeepPLM is our in-house contact-map prediction approach that has the 
   same deep-learning architecture as ResPRE, except it uses different 
   features that are generated by CCMpred.

5.2. How to install contact programs?

   When you unpack the C-QUARK Suite, ResTriplet, TripletRes, ResPRE, ResPLM and DeepPLM programs are already installed.

5.3. How to cite contact?

   If you are using the TripletRes program, you can cite:

   Yang Li, Chengxin Zhang, Eric W Bell, Wei Zheng, Dongjun Yu, Yang Zhang. 
   Deducing high-accuracy protein contact-maps from a triplet of 
   coevolutionary matrices through deep residual convolutional networks. 
   submitted, 2020.

   If you are using the ResTriplet program, you can cite:

   Yang Li, Chengxin Zhang, Eric W. Bell, Dongjun Yu, Yang Zhang.
   Ensembling multiple raw coevolutionary features with deep residual 
   neural networks for contact-map prediction in CASP13.
   Proteins: Structure, Function, and Bioinformatics, 87: 1082-1091 (2019).

   If you are using the ResPre program, you can cite:

   Yang Li, Jun Hu, Chengxin Zhang, Dong-Jun Yu, and Yang Zhang. 
   ResPRE: high-accuracy protein contact prediction by coupling precision 
   matrix with deep residual neural networks. Bioinformatics, 35: 4647-4655 (2019). 

   If you are using the ResPLM and DeepPLM programs, you can cite:

   Wei Zheng, Yang Li, Chengxin Zhang, Robin Pearce, S. M. Mortuza, Yang Zhang.
   Deep-learning contact-map guided protein structure prediction in CASP13.
   Proteins: Structure, Function, and Bioinformatics, 87: 1149-1164 (2019).


   #######################################################
   #                                                     #
   #  6. Installation and implementation of MUSTER       #
   #                                                     #
   #######################################################
   
6.1. Introduction of MUSTER
   
   MUSTER (MUlti-Sources ThreadER) is a protein threading algorithm to 
   identify the template structures from the PDB library. It generates 
   sequence-template alignments by combining sequence profile-profile 
   alignment with multiple structural information. The program will be run 
   only when you select QT/QP/QN/QTC/QPC/QNC mode.

6.2. How to install MUSTER program?

   When you unpack the C-QUARK Suite, MUSTER program is already installed.

6.3. How to cite MUSTER?

   If you are using the MUSTER program, you can cite:

   S Wu, Y Zhang. MUSTER: Improving protein sequence profile-profile 
   alignments by using multiple sources of structure information. 
   Proteins, 72: 547-556 (2008).

   #######################################################
   #                                                     #
   #  7. Installation and implementation of CEthreader   #
   #                                                     #
   #######################################################
   
7.1. Introduction of CEthreader

   CEthreader is a novel threading algorithm, which first predicts 
   residue-residue contacts by coupling evolutionary precision matrices with
   deep residual convolutional neural-networks. The predicted contact maps 
   are then integrated with sequence profile alignments to recognize 
   structural templates from the PDB. The program will be run 
   only when you select QT/QP/QN/QTC/QPC/QNC mode.

7.2. How to install CEthreader program?

   When you unpack the C-QUARK Suite, CEthreader program is already installed.

7.3. How to cite CEthreader?

   If you are using the CEthreader program, you can cite:

   W Zheng, Q Wuyun, Y Li, SM Mortuza, C Zhang, R Pearce, J Ruan, Y Zhang. 
   Detecting distant-homology protein structures by aligning deep 
   neural-network based contact maps. PLOS Computational Biology, 15: 
   e1007411 (2019).


   #######################################################
   #                                                     #
   #  8. Installation and implementation of LOMETS2       #
   #                                                     #
   #######################################################
   
8.1. Introduction of LOMETS2
   
   LOMETS2 (Local Meta-Threading-Server) is meta-server approach to protein
   fold-recognition. It consists of 11 individual threading programs: CEthreader, 
   mCEthreader, eCEthreader, MUSTER, PPA, dPPA, dPPA2, sPPA, wPPA, wdPPA, wMUSTER.
   The mCEthreader and eCEthreader are variances of CEthreader which includes 
   different scoring functions. The last 7 programs are variances of MUSTER 
   which includes different optimized energy terms. The program will be run 
   only when you select QT/QP/QN/QTC/QPC/QNC mode.

8.2. How to install LOMETS2 program?

   When you unpack the C-QUARK Suite, LOMETS2 programs are already installed.

8.3. How to cite LOMETS2?

   If you are using the LOMETS2 program, you can cite:

   Wei Zheng, Chengxin Zhang, Qiqige Wuyun, Robin Pearce, Yang Li, Yang Zhang. 
   LOMETS2: improved meta-threading server for fold-recognition and 
   structure-based function annotation for distant-homology proteins. 
   Nucleic Acids Research, 47: W429-W436 (2019)

   S Wu, Y Zhang. LOMETS: A local meta-threading-server for protein 
   structure prediction. Nucleic Acids Research, 35: 3375-3382 (2007).

   #######################################################
   #                                                     #
   #  9. Installation and implementation of DeepMSA      #
   #                                                     #
   #######################################################
   
9.1. Introduction of DeepMSA
   
   DeepMSA is a new open-source method for sensitive MSA construction, 
   which has homolo- gous sequences and alignments created from multi-sources 
   of whole-genome and metagenome databases through complementary hidden 
   Markov model algorithms. 

9.2. How to install DeepMSA program?

   When you unpack the C-QUARK Suite, DeepMSA program is already installed.

9.3. How to cite DeepMSA?

   If you are using the DeepMSA program, you can cite:

   C Zhang, W Zheng, S M Mortuza, Y Li, Y Zhang. DeepMSA: constructing 
   deep multiple sequence alignment to improve contact prediction and 
   fold-recognition for distant-homology proteins. Bioinformatics 36: 
   2105-2112 (2020). 
   
   #######################################################
   #                                                     #
   #  10. Installation and implementation of SPICKER     #
   #                                                     #
   #######################################################
   
10.1. Introduction of SPICKER
   
   SPICKER is a clustering algorithm to identify the near-native models 
   from a pool of protein structure decoys.

10.2. How to install SPICKER program?

   When you unpack the C-QUARK Suite, SPICKER program is already installed
   at $pkgdir/QUARKmod/spicker45d

10.3. How to run SPICKER program?

   To run SPICKER, you need to prepare following input files:
       'rmsinp'---Mandatory, length of protein & piece for RMSD calculation;
       'seq.dat'--Mandatory, sequence file, for output of PDB models.
       'tra.in'---Mandatory, list of trajectory names used for clustering.
                  In the first line of 'tra.in', there are 3 parameters:
                  par1: number of decoy files
                  par2: 1, default cutoff, best for decoys from template-based 
                           modeling; 
                       -1, cutoff based on variation, best for decoys from 
                           ab initio modeling.
                  par3: 1, closc from all decoys; -1, closc clustered decoys
                  From second lines are file names which contain coordinates
                  of 3D structure decoys. All these files are mandatory. See 
                  attached 'rep1.tra1' for the format of decoys.
       'CA'-------Optional, native structure, for comparison to native.

     Output files of SPICKER include:
       'str.txt'-----list of structure in cluster;
       'combo*.pdb'--PDB format of cluster centroids;
       'closc*.pdb'--PDB format of structures closest to centroids;
       'rst.dat'-----summary of clustering results;

    A detailed readme file can be found at
    http://zhanglab.dcmb.med.umich.edu/SPICKER/readme

10.4. How to cite SPICKER?

   If you are using the SPICKER program, you can cite:

   Y Zhang, J Skolnick, SPICKER: Approach to clustering protein structures 
   for near-native model selection, Journal of Computational Chemistry, 
   25: 865-871 (2004).


   #######################################################
   #                                                     #
   #  11. Installation and implementation of HAAD        #
   #                                                     #
   #######################################################
   
11.1. Introduction of HAAD
   
   HAAD is a computer algorithm for constructing hydrogen atoms from 
   protein heavy-atom structures. The hydrogen is added by minimizing 
   atomic overlap and encouraging hydrogen bonding. 

11.2. How to install HAAD program?

   When you unpack the C-QUARK Suite, HAAD program is already installed
   at $pkgdir/abs/mybin/HAAD

11.3. How to run HAAD program?

   Hydrogen atoms in a PDB file(xx.pdb) can be added by running 
   "./HAAD xx.pdb", the output is "xx.pdb.h".

   In "xx.pdb.h", the label in column 57 presents the label for the atoms 
   that have been added by HAAD. When the value of the label is less 
   than 2, the position of the added atom has higher confidence.

11.4. How to cite HAAD?

   If you are using the HAAD program, you can cite:

   Y Li, A Roy, Y Zhang, HAAD: A Quick Algorithm for Accurate Prediction 
   of Hydrogen Atoms in Protein Structures, PLoS One, 4: e6701 (2009).


   #######################################################
   #                                                     #
   #  12. Installation and implementation of EDTSurf     #
   #                                                     #
   #######################################################
   
12.1. Introduction of EDTSurf
   
   EDTSurf is a program to construct triangulated surfaces for macromolecules. 
   It generates three major macromolecular surfaces: van der Waals surface, 
   solvent-accessible surface and molecular surface (solvent-excluded 
   surface). EDTsurf also identifies cavities which are inside of 
   macromolecules. 

12.2. How to install EDTSurf program?

   When you unpack the C-QUARK Suite, EDTSurf program is already installed
   at $pkgdir/bin/EDTSurf

12.3. How to use EDTSurf program?

   EDTSurf -i inputfile ...
   Specific options:
         -o prefix of output files (default is the prefix of inputfile)
         -t triangulation type, 1-MC 2-VCMC (default is 2)
         -s surface type, 1-VWS 2-SAS 3-MS (default is 3)
         -c color mode, 1-pure 2-atom 3-chain (default is 2)
         -p probe radius, float point in [0,2.0] (default is 1.4)
         -h inner or outer surface for output, 1-inner and outer 2-outer 
	    3-inner (default is 1)
         -f scale factor, float point in (0,20.0] (default is 4.0)

      Molecule is scaled by this factor to fit in a bounding box. Scale 
      factor is the larger the better, but will increase the memory use. 
      Our strategy is first enlarging the molecule to check if it exceeds 
      the maximum bounding box. If yes, then reset a proper scale factor 
      to fit the molecule in the maximum bounding box.

   By running EDTSurf itself, it will print out a brief description on how
   to use the program. A detail description of EDTSurf is available at
   http://zhanglab.dcmb.med.umich.edu/EDTSurf/

12.4. How to cite EDTSurf?

   If you are using the EDTSurf program, you can cite:

   D Xu, Y Zhang, Generating Triangulated Macromolecular Surfaces by Euclidean 
   Distance Transform. PLoS ONE 4: e8140 (2009).


   #######################################################
   #                                                     #
   #  13. Installation and implementation of ModRefiner  #
   #                                                     #
   #######################################################
   
13.1. Introduction of ModRefiner
   
   ModRefiner is a standalone program for atomic-level protein structure 
   construction and refinement. It includes two steps: (1) construct
   main-chain models from C-alpha trace; (2) build side-chain models
   and atomic-level structure refinement.

13.2. How to install ModRefiner program?

   When you unpack the C-QUARK Suite, ModRefiner program is already installed
   at $pkgdir/QUARKmod/ModRefiner.pl

13.3. How to use ModRefiner program?

   ModRefiner supports following four options:
   
   a) add side-chain heavy atoms to main-chain model without refinement
      > ModRefiner.pl 1 ID MD IM ON

   b) build main-chain model from C-alpha trace model
      > ModRefiner.pl 2 ID MD IM RM ON

   c) build full-atomic model from main-chain model
      > ModRefiner.pl 3 ID MD IM RM ON

   d) build full-atomic model from C-alpha trace model
      > ModRefiner.pl 4 ID MD IM RM ON

   ID: the path of the C-QUARK package, e.g. '/home/yourname/C-QUARK-1.0'
   MD: directory which contains the initial model, e.g. '/home/yourname/C-QUARK/5.0/example'
   IM: the initial model to be refined, e.g. 'mode1.pdb'
   RM: reference model that refined model is driven to, e.g. 'combo1.pdb'.
       Only CA trace is needed and the length can be not full which will make 
       the refinement of the missing region flexible. If you don't have the
       reference model, use the name of IM instead.
   ON: the output name of the refined model, e.g. 'model1_ref.pdb'

   By running the program without argument, you can print a brief description
   of how to use the program.
   
13.4. How to cite ModRefiner?

   If you are using the ModRefiner program, you can cite:

   D Xu, Y Zhang. Improving the Physical Realism and Structural Accuracy of 
   Protein Models by a Two-step Atomic-level Energy Minimization. 
   Biophysical Journal, 101: 2525-2534 (2011)


   #######################################################
   #                                                     #
   #  14. Installation and implementation of NWalign     #
   #                                                     #
   #######################################################
   
14.1. Introduction of NWalign
   
   NW-align is simple and robust alignment program for protein 
   sequence-to-sequence alignments based on the standard Needleman-Wunsch 
   dynamic programming algorithm. The mutation matrix is from BLOSUM62 
   with gap opening penalty=-11 and gap extension penalty=-1. 

14.2. How to install NWalign program?

   When you unpack the C-QUARK Suite, NWalign program is already installed
   at $pkgdir/bin/align.

14.3. How to use NWalign program?
   
   > align F1.fasta F2.fasta (align two sequences in fasta file)
   > align F1.pdb F2.pdb 1   (align two sequences in PDB file)
   > align F1.fasta F2.pdb 2 (align Sequence 1 in fasta and 2 in pdb)
   > align GKDGL EVADELVSE 3 (align sequences typed by keyboard)
   > align GKDGL F.fasta 4   (align Seq-1 by keyboard and 2 in fasta)
   > align GKDGL F.pdb 5     (align Seq-1 by keyboard and 2 in pdb)

   By running the program itself, it will print out the usage options of
   the program.

14.4. How to cite NWalign?

   There is no published paper associated with this program. If you are using
   the NWalign program, you can cite it as 

   Y Zhang, http://zhanglab.dcmb.med.umich.edu/NW-align


   #######################################################
   #                                                     #
   #  15. Installation and implementation of PSSpred     #
   #                                                     #
   #######################################################
   
15.1 Introduction of PSSpred

   PSSpred (Protein Secondary Structure PREDiction) is a simple neural network 
   training algorithm for accurate protein secondary structure prediction. It first 
   collects multiple sequence alignments using PSI-BLAST. Amino-acid frequency and 
   log-odds data with Henikoff weights are then used to train secondary structure, 
   separately, based on the Rumelhart error back propagation method. The final 
   secondary structure prediction result is a combination of 7 neural network 
   predictors from different profile data and parameters.

15.2 How to install PSSpred program?

   When you unpack the C-QUARK Suite, NWalign program is already installed
   at $pkgdir/PSSpred
   
15.3 How to use PSSpred program?   

   $pkgdir/PSSpred/mPSSpred.pl seq.txt $pkgdir $libdir

   Please note that 'seq.txt' should be in current directory and the script will
   generate two files 'seq.dat' and 'seq.dat.ss' in the current folder. Here, 
   $pkgdir is the root path of C-QUARK package.
 
15.4 How to cite PSSpred?

   If you are using the PSSpred program, you can cite:
   http://zhanglab.dcmb.med.umich.edu/PSSpred


   #######################################################
   #                                                     #
   #  16. Installation and implementation of ResQ        #
   #                                                     #
   #######################################################
   
16.1 Introduction of ResQ
  
   ResQ is a method for estimating B-factor and residue-level quality in protein
   structure prediction, based on local variations of modelling simulations and 
   the uncertainty of homologous alignments. Given a protein structure model, 
   ResQ first identifies a set of homologous and/or analogous templates from 
   the PDB by threading and structure alignment techniques. The residue-level 
   modeling errors are then derived by support vector regression that was 
   trained on the local structural and alignment variations of the templates, 
   with the B-factor of each residue deduced from the experimental records of 
   the top homologous proteins. 

16.2 How to install ResQ program?

   When you unpack the C-QUARK Suite, ResQ program is already installed at 
   $pkgdir/ResQ.
   
16.3 How to use ResQ program?

   There are two methods to run ResQ depending on how your models were generated.
      1) If your models were generated by C-QUARK, you can run the script of 
         $pkgdir/ResQ/runResQ_IT.pl to predict B-factor and local structure errors. 
         The only argument required is the directory of the C-QUARK decoys. You 
         can read more at the head of this script to get more information about 
         its input.

      2) If your models were not generated by C-QUARK, you can run the script 
         $pkgdir/ResQ/runResQ.pl to predict B-factor and local structure errors. 
     It will automatically run LOMETS2 to generate the threading alignment 
     file 'init.dat'. LOMETS2 is included in this package.

16.4 What is the output of ResQ?

     For C-QUARK models, the output of ResQ is: 
         rsq_bfp_new.dat

     For other models, the output of ResQ is:
         1) global.txt for global accuracy estiamtion
         2) local.txt for local error and B-factor estimation

16.5 How to cite ResQ?

   If you are using the ResQ program, you can cite:

   1. J Yang, Y Wang, Y Zhang. ResQ: Approach to unified estimation of B-factor 
      and residue-specific error in protein structure prediction, 
      Journal of Molecular Biology, 428: 693-701 (2016).
