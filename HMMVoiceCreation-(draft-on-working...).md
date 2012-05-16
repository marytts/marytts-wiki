HMMVoiceCreation for MARY 5.0

We use the training scripts provided by [HTS](http://hts.sp.nitech.ac.jp/) adapted to the MARY 5.0 platform.
The steps for building a HMM voice for the MARY platform can be summarised in:  
1. [Download MARY TTS including Voice import tools](https://github.com/marc1s/marytts#readme)  
2. [Check necessary programs and files][2. Check the necessary programs and files:]  
3. Check data: audio and text files  
4. Run the Voice import tools  
5. Creating other voice in a language different from German or English (US).  
6. Adaptive scripts  

[2. Check the necessary programs and files:]  
We provide an script to facilitate the checking and installation of the necessary external programs, once installed MARY TTS open a command line shell in your voice building directory and run the shell script:

`$MARY_BASE/lib/external/check_install_external_programs.sh`

With the option -check, this script will check if the necessary programs and versions are installed (that is, the programs can be found in the PATH or in the paths provided by the user).
With the option -install this script will try to download and install the necessary programs in: $MARY_BASE/lib/external/bin (if problems, it will suggest how to install manually the programs).

If you have already installed some of the required programs, please include their paths in the PATH variable or provide the paths, for example:

`$MARY_BASE/lib/external/check_install_external_programs.sh -check /your/path/to/htk/bin /your/path/to/Festival/festvox/src/ehmm/bin`

This script generates a $MARY_BASE/lib/external/externalBinaries.config file that will be used by the Voice import tools to locate the necessary external programs.

The necessary programs that this script checks are:  
HTS requirements:  
[HTS-2.2_for_HTK-3.4.1.patch ](http://hts.sp.nitech.ac.jp/archives/2.2/HTS-2.2_for_HTK-3.4.1.tar.bz2)  
HTK-3.4.1 and HDecode patched with HTS-2.2_for_HTK-3.4.1.patch links:  
[HTK-3.4.1](http://htk.eng.cam.ac.uk/ftp/software/HTK-3.4.1.tar.gz) (you will need to register first)  
[HDecode](http://htk.eng.cam.ac.uk/prot-docs/hdecode.shtml) (you will need to register first)  
[SPTK-3.4.1](http://downloads.sourceforge.net/sp-tk/SPTK-3.4.1.tar.gz)  
[hts_engine_API-1.05](http://downloads.sourceforge.net/hts-engine/hts_engine_API-1.05.tar.gz)  

Other requirements:  
awk normally available in linux  
perl normally available in linux  
bc normally available in linux  
sox, v13.0 or greater [SoX](http://sox.sourceforge.net/), normally available in linux.  
tcl supporting snack, for example [ActiveTcl](http://www.activestate.com/Products/ActiveTcl/). Note that only ActiveTcl 8.4 includes snack; 8.5+ requires manual installation.  
[snack](http://www.speech.kth.se/snack/download.html) library for tcl.  
EHMM for automatic labeling, available with [festvox-2.1](http://festvox.org/download.html)  



