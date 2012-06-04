HMMVoiceCreation for MARY 5.0

We use the training scripts provided by [HTS](http://hts.sp.nitech.ac.jp/) adapted to the MARY 5.0 platform.
The steps for building a HMM voice for the MARY platform can be summarised in:  
I. [Download MARY TTS including Voice import tools](https://github.com/marc1s/marytts#readme)  
II. [Required programs and files](#stepII)  
III. [Preparing data for training a HMM-voice](#stepIII)  
IV. [Training a HMM-voice](#stepIV)   
V. [Adding a new HMM-based voice in MARY platform](#stepV)  
VI. [Training a HMM-voice in other language](#stepVI)  

***

## <a name="stepII" /> II. Required programs and files
We provide an script to facilitate the checking and installation of the necessary external programs, once MARY TTS has been installed, open a command line shell in your voice building directory and run the shell script:
 
    $MARY_BASE/lib/external/check_install_external_programs.sh

With the option -check, this script will check if the necessary programs and versions are installed (that is, the programs can be found in the PATH or in the paths provided by the user).
With the option -install this script will try to download and install the necessary programs in: $MARY_BASE/lib/external/bin (if problems, it will suggest how to install manually the programs).

If you have already installed some of the required programs, please include their paths in the PATH variable or provide the paths, for example:

    $MARY_BASE/lib/external/check_install_external_programs.sh -check /your/path/to/htk/bin /your/path/to/Festival/festvox/src/ehmm/bin

This script generates a $MARY_BASE/lib/external/externalBinaries.config file that will be used by the Voice import tools to locate the necessary external programs.

The necessary programs that this script checks are:  
**HTS requirements:**  
* [HTS-2.2_for_HTK-3.4.1.patch ](http://hts.sp.nitech.ac.jp/archives/2.2/HTS-2.2_for_HTK-3.4.1.tar.bz2)  
* HTK-3.4.1 and HDecode patched with HTS-2.2_for_HTK-3.4.1.patch links:  
* [HTK-3.4.1](http://htk.eng.cam.ac.uk/ftp/software/HTK-3.4.1.tar.gz) (you will need to register first)  
* [HDecode](http://htk.eng.cam.ac.uk/prot-docs/hdecode.shtml) (you will need to register first)  
* [SPTK-3.4.1](http://downloads.sourceforge.net/sp-tk/SPTK-3.4.1.tar.gz)  
* [hts_engine_API-1.05](http://downloads.sourceforge.net/hts-engine/hts_engine_API-1.05.tar.gz)  

**Other requirements:**  
* awk normally available in linux  
* perl normally available in linux  
* bc normally available in linux  
* sox, v13.0 or greater [SoX](http://sox.sourceforge.net/), normally available in linux.  
* tcl supporting snack, for example [ActiveTcl](http://www.activestate.com/Products/ActiveTcl/). Note that only ActiveTcl 8.4 includes snack; 8.5+ requires manual installation.  
* [snack](http://www.speech.kth.se/snack/download.html) library for tcl.  
* EHMM for automatic labeling, available with [festvox-2.1](http://festvox.org/download.html)  

***

##<a name="stepIII" /> III. Preparing data for training a HMM-voice
In your voice building directory execute the step-by-step procedure in [VoiceImportToolsTutorial](http://mary.opendfki.de/wiki/VoiceImportToolsTutorial) to make sure that the data, sound (wav) and text files are in the correct place and format. As a result of this step your voice building directory should contain a wav and text directories. 

In your voice building directory run the voice import tools:

    $MARY_BASE/target/marytts-builder-<VERSION>/bin/voiceimport.sh

And run the following components:

1- Run the **AllophonesExtractor** to create the prompt_allophones directory required in the next step. This component requires the MARY server.

2- Run the **EHMMlabeler** to label automatically the wav files using the corresponding transcriptions. If the pauses at the beginning and end of your recordings are longer than 0.2 seconds, you might consider to reduce these pauses using the tool: Convert recorded audio (as explained in NewLanguageSupport No. 9) to trim initial and final silences.

The EHMMLabeler procedure might take several hours. For running EHMMLabeler, please use the settings editor of this component to set, according to your festvox installation, the variable:

    EHMMLabeler.ehmm  = ../festvox/src/ehmm/bin/

The result of this step is a ehmm/lab directory.

3- Run the **LabelPauseDeleter**. Please use the settings editor of this component to set the variable:

    LabelPauseDeleter.threshold  =  10

The result of this step is a lab directory.

4- Run the **PhoneUnitLabelComputer**, this procedure has as input the lab directory and will create as an output the phonelab directory.

5- Run the **TranscriptionAligner**, this program will create the allophones directory.

6- Run the **FeatureSelelection**, this program will create a mary/features.txt file, it requires the MARY server running. Select here all the features and save the file.

7- Run the **PhoneUnitFeatureComputer**, to extract context feature vectors from the text data. This procedure will create a phonefeatures directory. For running this component the MARY server should be running as well.

8- Run the **PhonelabelFeatureAligner**, this procedure will verify alignment between "phonefeatures" and "phonelabels".

As a result of previous steps you should have in your voice building directory:

    phonefeatures directory
    phonelab directory
    mary/features.txt file
    $MARY_BASE/lib/external/externalBinaries.config 

***
##<a name="stepIV" /> IV. Training a HMM-voice

9- Run the **HMMVoiceDataPreparation** to set up the environment to create a HMM voice and check if the required external programs, text and wav files are available and in the correct paths.

10- Run the **HMMVoiceConfigure**, the default setting values are already fixed for the arctic slt voice, some path settings depend on your installation, and will be taken from $MARY_BASE/lib/external/externalBinaries.config

If running configure for other voice, for example a male German voice, please use the settings editor of this component to set the variables:

    HMMVoiceConfigure.dataSet      =  german_set_name
    HMMVoiceConfigure.speaker      =  speaker_name 
    HMMVoiceConfigure.lowerF0      =  40  (male=40,  female=80)  
    HMMVoiceConfigure.upperF0      =  280 (male=280, female=350)

Using the settings editor of this component you can also change other variables like using LSP instead of MGC, sampling frequency, etc., the same as you would do when running "make configure + parameters" with the original HTS scripts.

11- Run the **HMMVoiceFeatureSelection**, this program reads the mary/features.txt file (created in step 6), and generates the file mary/hmmFeatures.txt. The hmmFeatures.txt file contains extra features, apart from phone and phonological features, that will be used to train HMMs. You can select or delete on the window extra context features (all can be used).

12- Run the **HMMVoiceMakeData**, to run the HTS procedure "make data". The HMMVoiceMakeData procedure is similar to the original HTS scripts with additional sections for calculating voicing strengths (str), for mixed excitation, and composing training data files from mgc, lf0 and str files. This component executes in the hts/data/ directory:

    make mgc lf0 str-mary cmp-mary list scp 

The label directory and the mlf files in MARY are done in java with the Voice Import Tools: HMMVoiceMakeData.makeLabels()
The questions file in MARY is done in java with the Voice Import Tools: HMMVoiceMakeData.makeQuestions()

Particular procedures can be repeated isolated fixing the particular settings for this component. For example, if the procedure that creates strengths (in the str directory) has to be repeated with a different set of filters (various sets of filter are provided in hts/data/filters/), set:

    HMMVoiceMakeData.makeSTR       =  1
    HMMVocieMakeData.makeCMPMARY   =  1

all the other variables in 0, and run again the component. (In this case you need to run makeCMPMARY again because you need to compose again the vectors mgc+lf0+str).

The procedures can be repeated manually as well, going to the hts/data directory and running "make str-mary" and "make cmp-mary".

13- Run the **HMMVoiceMakeVoice**, here again particular training steps can be repeated selecting them (setting in 1, all the others in 0) from the settings of this component. This is equivalent to run (on the comman line):

    perl scripts/Training.pl scripts/Config.pm > logfile &

as is normally done with the original HTS scripts. This component will generate general information about the execution of the training steps. Detailed information about the training status can be found in the logfile in the current directory.

The training procedure can take several hours, please check the log file time to time to check progress. 

***

##<a name="stepV" /> V. Adding a new HMM-based voice in MARY platform

14- Run the **HMMVoiceCompiler** to compile the voice to be used in MARY TTS. The default setting values of this component are already fixed.  
Once the voice is compiled, follow the instructions in [Publishing-a-MARY-TTS-Voice](https://github.com/marytts/marytts/wiki/Publishing-a-MARY-TTS-Voice) to install the voice.

***

##<a name="stepVI" /> VI. Training a HMM-voice in another language

If you are creating a voice in other language you will need to specify:

**Minimal NLP components**: if you are creating a new voice from scratch, for example following the steps in [NewLanguageSupport](https://github.com/marytts/marytts/wiki/New-Language-Support), you will need to create Minimal NLP components for the new language. These minimal components are necessary to run the MARY server in the new language and extract context features (phonefeatures directory). 

**Phoneme set**: contained in $MARY_BASE/marytts-lang-xx/src/main/resources/marytts/language/xx/lexicon/allophones.xx.xml , where xx corresponds to the new language. 

After creating the minimal components, you will need wave files (in a wav directory) and the corresponding transcriptions (one file per wave file in a text directory).

Afterwards follow the instructions as normal from step 1. Provide general settings for:

    db.gender    =  male  (or female)
    db.locale    =  new_language locale (according to your minimal NLP components, ex. tr for Turkish, te for Telugu, etc.)
    db.marybase  =  /path/to/mary/base/
    db.voicename =  new_language_voice_name




