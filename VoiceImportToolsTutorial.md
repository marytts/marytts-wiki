# Voice Import Tools Tutorial : How to build a new Voice with Voice Import Tools

This Tutorial explains the procedure to build a new voice with Voice Import Tools (VIT) under the MARY Environment. 
Voice Import Tool is a Graphical User Interface (GUI), which contains a set of Voice Import Components and helps the user to build new voices under the MARY (Modular Architecture for Research in speech sYnthesis) Environment. This GUI tool aims to simplify the task of building new synthesis voices so that users who do not have detailed technical knowledge of speech synthesis can build their own voices.

In a nutshell, the Voice Import Tools cover the following steps in voice building:  
1. Feature Extraction from Acoustic Data  
2. Feature Vector Extraction from Text Data  
3. Automatic Labeling  
4. [Unit Selection voice building](https://github.com/marytts/marytts/wiki/UnitSelectionVoiceCreation)  
5. [HMM-based voice building](https://github.com/marytts/marytts/wiki/HMMVoiceCreation)  
6. [Publishing-a-MARY-TTS-Voice](https://github.com/marytts/marytts/wiki/Publishing-a-MARY-TTS-Voice)  

**Requirements:**  
- Operating System - Linux (Strongly recommended)  
- MARY TTS Recent Version - [Download MARY TTS including Voice import tools](https://github.com/marytts/marytts/releases)  
- (be sure Java 7 or higher is already installed – else type “sudo apt-get install openjdk-7-jdk” or visit https://www.java.com/en/download/)

(You may be able to use other operating systems such as Mac OS X or Windows if you can get the dependencies to work, which are described below) 

**Dependent Tools:**  
- Praat - For pitch marks. On Ubuntu-like systems, install the praat package by typing “sudo apt-get install praat” into the command line. Otherwise, download [Praat](http://www.fon.hum.uva.nl/praat).
- Edinburgh Speech Tools – For MFCCs and Wagon (CART). On Ubuntu-like systems, install the speech-tools package (“sudo apt-get install speech-tools”). Otherwise, download and install manually [Speech Tools](http://www.cstr.ed.ac.uk/projects/speech_tools/). The path for this tool can be provided with the general settings:  

    db.estDir  : /path/to/Festival/speech_tools/

- EHMM – For Automatic Labeling, EHMM is available in $MARYBASE/lib/external/ehmm.tar.gz, or with Festvox version 2.1 or newer:  http://festvox.org/download.html. Once the EHMM binaries have been built, the path to the EHMM bin directory must be provided to MARY. The path for this tool can be provided on the EHMM component settings:   

        EHMMLabeler.ehmmDir  : /path/to/ehmm/
 
## Voice Import Components: 

<center>![VoiceImport Tools GUI](https://mary.opendfki.de/trac/raw-attachment/wiki/VoiceImportToolsTutorial/VIC1.jpg)</center>
                         
The components in the GUI are organised according to:  
**Raw Acoustics:**`PraatPitchmarker` `MCEPMaker`  
**Transcripts Conversion:**`  Festvox2MaryTranscripts`  
**Automatic Labelling:**`AllophonesExtractor` `EHMMLabeler` `LabelPauseDeleter` `LabelledFilesInspector`  
**Label-Transcript Alignment:** `PhoneUnitLabelComputer` `HalfPhoneUnitLabelComputer` `TranscriptionAligner`  
**Feature Extraction:** `FeatureSelection` `PhoneUnitFeatureComputer` `HalfPhoneUnitFeatureComputer`  
**Verify Alignment:** `PhoneLabelFeatureAligner` `HalfPhoneLabelFeatureAligner`  
**Basic Data Files:** `WaveTimelineMaker` `BasenameTimelineMaker` `MCepTimelineMaker`  
**Acoustic Models:** `PhoneUnitfileWriter` `PhoneFeatureFileWriter` `DurationCARTTrainer` `F0CARTTrainer`  
**Unit Selection Files:** `HalfPhoneUnitfileWriter` `HalfPhoneFeatureFileWriter` `F0PolynomialFeatureFileWriter` `AcousticFeatureFileWriter` `JoinCostFileMaker` `CARTBuilder`  
**HMM Voice Trainer:** `HMMVoiceDataPreparation` `HMMVoiceConfigure` `HMMVoiceFeatureSelection` `HMMVoiceMakeData` `HMMVoiceMakeVoice`  
**Install Voice:** `VoiceCompiler` `HMMVoiceCompiler`  


## Step-by-Step Procedure

The two basic requirements for building a voice are:  
a. Wave files  
b. Corresponding Transcription (in MARY or Festvox Format):  
- MARY Format: Each transcription represented by a single file. All these files are placed in a single directory (by default, 'text' directory of voice-building directory).  
- Festvox (Festival) Format : A single file contains all transcriptions, example:  

        ( arctic_a0001 "AUTHOR OF THE DANGER TRAIL, PHILIP STEELS, ETC" )
        ( arctic_a0002 "Not at this particular case, Tom, apologized Whittemore." )
        ( arctic_a0003 "For the twentieth time that evening the two men shook hands." )
        ( arctic_a0004 "Lord, but I'm glad to see you again, Phil." )
        ( arctic_a0005 "Will we ever forget it." )

### 1. Create a new Voice Building Directory

Create a voice building directory somewhere on your file system, say /path/myvoice.  
- Put all Wave files into the wav sub-directory of the voice building directory, i.e. /path/myvoice/wav. You may want to use the Audio converter GUI to make sure that this data is mono, at the right sampling rate, doesn't include overly long initial and final pauses, etc.  
- Either put the individual text files into /path/myvoice/text subdirectory (if using text files in MARY format), or the single text file in Festvox format into /path/myvoice/txt.done.data.  

If you want to test this but haven't recorded your own voice files yet, one way of getting data to test this is to use the ARCTIC data from CMU. Download and unpack http://www.speech.cs.cmu.edu/cmu_arctic/packed/cmu_us_slt_arctic-0.95-release.tar.bz2, and then:  

    copy or move cmu_us_slt_arctic/wav to myvoice/wav, including all wav files;
    copy or move cmu_us_slt_arctic/etc/txt.done.data to myvoice/txt.done.data. 

### 2. Run the voice building tools  
After downloading MARY TTS (and running "mvn install"), run the voice import tools in your voice building directory with:  

    $MARY_BASE/target/marytts-builder-<VERSION>/bin/voiceimport.sh  

**Global Configuration Settings**  
When you are running the voice building tools for the first time, it asks you general configuration settings like:  

    estDir       : /your/path/to/Festival/speech_tools (which is /usr by default; important when creating unit selection voices)
    gender       : male or female 
    locale       : depends on your voice language, it can be: en_US, de, etc. 
    marybase     : /your/path/to/marytts/
    samplingrate : 16000
    voicename    : my_voice  
  
All other settings depend on your voice building path or are default values that are filled automatically.  

After filling in general settings  and clicking the **Save** button, you will get to the main window of the Voice Import Tools. There you can see a list of components which in general are supposed to be run sequentially. A component is executed by ticking the associated checkbox and clicking on **Run**. 

**Component Configuration Settings**  

You can verify and change the settings for each individual component by clicking on the wrench symbol next to the component. Clicking on **Settings** takes you to the window where you can change the general settings.  
There is available a help for each component (in some cases this help might be out-dated).
 
The voice import tool creates two files in the directory where you started it:    
- **importMain.config:** contains a list of voice import components presented on the GUI
- **database.config:** contains the values of the settings for each component - you can change the settings also in this file.  
 
**How to run the Voice Import Components**

There is a sequence of steps for creating a unit selection or a hmm-based voice. Ideally one would need to simply run each step after the other. However, the user is often required to make a few decisions here and there. So the real-world process is usually a bit more complex than that.  
For example:  
- when using a pitch marker, you may want to verify that the frequency range is appropriate for your recordings, and adapt the component's config settings before running it again.  
- if your transcriptions are in Festvox format, it is necessary to choose "Festvox2MaryTranscripts" Component. This will convert the transcriptions from Festvox format (txt.done.data) into MARY format (text/*.txt). Voice Import Tools uses MARY format transcription for building a voice.  
- if you have recorded your voice using Redstart, there is no need to run the "Mary2FestvoxTranscripts" component. 
- when using the labeler (under “Automatic Labeling” in the GUI) make sure to use either EHMMLabeler or HTKLabeler, not both.

(For more details, see the respective sections on voice creation with UnitSelection and HMM.)

While executing each component, a progress bar shows the percentage of work completed for that component. A component is converted to GREEN if that component is executed successfully. It turns RED, and it throws an exception, if that component encounters an error. If you get there, you will need to understand what went wrong, and how it must be fixed. There is no simple recipe for that case. 


An explanation about Individual Voice Import Components and how to create a new **Unit selection voice** can be found here:  
- [UnitSelectionVoiceCreation](https://github.com/marytts/marytts/wiki/UnitSelectionVoiceCreation)

An explanation about how to create a new **HMM-based voice** can be found here:  
- [HMMVoiceCreation](https://github.com/marytts/marytts/wiki/HMMVoiceCreation).
