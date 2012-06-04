# Unit selection voice creation and explanation on Individual Voice Import Components

Steps to create a unit selection voice:  
[1. Feature Extraction from Acoustic Data](#step1)    
[2. Support for Transcription Conversion](#step2)  
[3. Automatic Labeling](#step3)  
[4. Label-transcription Alignment](#step4)  
[5. Feature Vector Extraction from Text Data](#step5)  
[6. Verify Alignment](#step6)  
[7. Basic Data Files](#step7)  
[8. Building acoustic models](#step8)  
[9. Unit Selection Files](#step9)  
[10. Adding a new unit selection voice in MARY](#step10)  


### <a name="step1" /> 1. Feature Extraction from Acoustic Data

**PraatPitchmarker**  
It computes pitch markers with help of Praat. You need to compile or install Praat in your machine.  
It also do corrections for Pitch Marks to align near by Zero Crossing.  
Configuration Settings:  

    command   : Give absolute path of Praat Executable  
                (Note for Mac OS users: this should be /Applications/Praat.app/Contents/MacOS/Praat)  
    pmDir     : Output Dir Path for Praat Pitch marks  
    corrPmDir : Output Dir Path for corrected pitch marks (Pitch marks tuned towards Zero Crossing)  
    maxPitch, minPitch : For choosing Pitch Range (Ex: Male: 50-200 | Female: 150-300)  

**2 MCEPMaker**  
It calculate MFCCs from Speech Wave files, using Edinburgh Speech Tools.  
Configuration Settings:  

    mcepDir   : Output Dir for MFCCs  

This component uses the Edinburgh Speech Tools, the path for this tool can be set on the general settings:  

    estDir       : /your/path/to/Festival/speech_tool 


### <a name="step2" /> 2. Support for Transcription Conversion

**Festvox2MaryTranscripts** (optional)  
This Component converts Festvox Transcription format (ex: txt.done.data) into MARY format, that is one transcription file per sentence. 
Configuration Settings:  

    transcriptFile - Festvox format transcription file (Absolute path) 

The output of this component is a text directory in your voice building directory, containing one transcription file per sentence.  

### <a name="step3" /> 3. Automatic Labeling

**AllophonesExtractor** 
creates the prompt_allophones directory required in the next step. This component requires the MARY server.

**EHMMlabeler**  
labelss automatically the wav files using the corresponding transcriptions. If the pauses at the beginning and end of your recordings are longer than 0.2 seconds, you might consider to reduce these pauses using the tool: Convert recorded audio (as explained in NewLanguageSupport No. 9) to trim initial and final silences.

The EHMMLabeler procedure might take several hours. For running EHMMLabeler, please use the settings editor of this component to set, according to your festvox installation, the variable:

    EHMMLabeler.ehmm  = ../festvox/src/ehmm/bin/

The result of this step is a ehmm/lab directory.

### <a name="step4" /> 4. Label-transcription Alignment  

**PhoneUnitLabelComputer**  

**HalfPhoneUnitLabelComputer**

**TranscriptionAligner**  

### <a name="step5" /> 5. Feature Vector Extraction from Text Data
FeatureSelection  
PhoneUnitfeatureComputer  
HalfPhoneUnitfeatureComputer  

### <a name="step6" /> 6. Verify Alignment  
PhoneLabelfeatureAlignment
HalfPhoneLabelfeatureAlignment

### <a name="step7" /> 7. Basic Data Files  
WaveTimelineMaker  
BasenameTimelineMaker  
MCepTimelineMaker  

### <a name="step8" /> 8. Building acoustic models  
PhoneUnitfileWriter  
PhoneFeatureFileWriter  
DurationCARTTrainer  
F0CARTTrainer  

### <a name="step9" /> 9. Unit Selection Files  

**HalfPhoneUnitfileWriter**  
It produces a file containing all halfphone sized units. Configuration Settings:

    corrPmDir - directory containing the corrected pitchmarks
    labelDir - directory containing the halfphone labels
    unitFile - file containing all halfphone units. Will be created by this module 

**HalfPhoneFeatureFileWriter**  
It produces a file containing all the target cost features for the phone sized units. The module needs a file defining which features are to be used and what weights are given to them. They must be the same features as the ones that the HalfPhoneFeatureComputer used. If you do not have a feature definition, the module tries to create one.  

For more information, see the example file: Marybase/lib/modules/import/examples/HalfPhoneUnitFeatureDefinition.txt  CHECK!

Configuration Settings:

    featureDir - directory containing the halfphone features
    featureFile - file containing all halfphone units and their target cost features.Will be created by this module
    unitFile - file containing all halfphone units
    weightsFile - file containing the list of halfphone target cost features, their values and weights 

**JoinCostFileMaker**  
It produces a file containing all the join cost features for the halfphone sized units. Configuration Settings:

    joinCostFile - file containing all halfphone units and their join cost features. Will be created by this module
    mcepDir - directory containing the mcep files
    mcepTimeline - file containing all mcep files
    unitFile - file containing all halfphone units
    weightsFile - file containing the list of join cost weights and their weights 

**AcousticFeatureFileWriter**  
It produces a file containing all the target cost features plus two acoustic target cost features for the halfphone sized units. Also produces a feature definition containing those features.

    acFeatDef - file containing the list of phone target cost features, their values and weights
    acFeatureFile - file containing all halfphone units and their target cost features plus the acoustic target cost features. Will be created by this module.
    featureFile - file containing all halfphone units and their target cost features
    unitFile - file containing all halfphone units
    waveTimeLine - file containing all wave files 

**CARTBuilder**  
It builds a preselection tree for the target cost features using "wagon" (CART) from the Edinburgh Speech tools.  
Additionally, User need to specify either a feature sequence or a top level tree. They are used to built a basic tree that is extendend by wagon. This way, wagon runs several times on smaller subsets of units rather than the whole set. It might still take some time to run this module.

    Feature sequence: A file containing a list of features for which to build the tree.
    Top level tree: A file containing the basic tree. 

For more information on these two possibilities of specifying the basic tree, see the example files in Marybase/lib/modules/import/examples/  CHECK!

If you give the CARTBuilder neither a feature sequence nor a top level tree file, a default feature sequence is created which only contains "mary_phoneme" as feature. If the basic tree contains leaves that are contain more units than the maximum number of units allowed, the leaves are pruned and a warning message is printed. It is recommended that you make sure that there are no leaves that are too big.  
Configuration Settings:

    acFeatureFile - file containing all halfphone units and their target cost features plus the acoustic target cost features
    cartFile - file containing the preselection CART. Will be created by this module
    estDir - directory containing the local installation of the Edinburgh Speech Tools
    featureSeqFile - file containing the feature sequence for the basic tree
    maxLeafSize - the maximum number of units in a leaf of the basic tree
    mcepTimeline - file containing the mcep files
    readFeatureSequence - if "true", basic tree is read from feature sequence file; if "false", basic tree is read from top level tree file.
    topLevelTreeFile - file containing the basic tree
    unitFile - file containing all halfphone units 

**CARTPruner**  
It prunes the preselection tree and this module also removes outliers from the preselection tree. Configuration Settings:

    cartFile - file containing the preselection CART
    prunedCartFile - file containing the pruned preselection CART. Will be created by this module
    unitFeatureFile - file containing all halfphone units and their target cost features
    unitFile - file containing all halfphone units
    waveFile - file containing all wave files 


### <a name="step10" /> 10. Adding a new unit selection voice in MARY 
**VoiceCompiler** compiles the voice to be used in MARY TTS. The default setting values of this component are already fixed.  
Once the voice is compiled, follow the instructions in [Publishing-a-MARY-TTS-Voice](https://github.com/marytts/marytts/wiki/Publishing-a-MARY-TTS-Voice) to install the voice.

