# Unit selection voice creation and explanation on Individual Voice Import Components

Unit-Selection Voicebuilding has some prerequisites:
* all speech signals are in .wav format, 16kHz sampling frequency and 16-bit sample format.
* all speech signals have corresponding text of what is spoken in each speech signal (.txt format)
* maryTTS server must be running on localhost:59125(default)

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
It computes pitch markers with the help of Praat. You need to compile or install Praat on your machine. It is available for all major operating systems at this [link.](http://www.fon.hum.uva.nl/praat/)
It also does corrections for aligning Pitch Marks to the nearest Zero Crossings.  
Configuration Settings:  
    `wavDir : location of .wav files of utterances`  
    `command : Give absolute path of Praat Executable`  
        `(Note for Mac OS users: this should be /Applications/Praat.app/Contents/MacOS/Praat)`  
    `pmDir : Output Dir Path for Praat Pitch marks`  
    `maxPitch, minPitch : For choosing Pitch Range (Ex: Male: 50-200 | Female: 150-300)`  

**MCEPMaker**  
It calculates MFCCs from Speech Wave files, using Edinburgh Speech Tools.  
For Ubuntu users: `sudo apt-get install speech-tools` install directory defaults to /usr/   
For Mac OSX users: `brew install speech-tools`    
The path for this tool can be set on the general settings:  

    estDir : /your/path/to/Festival/speech_tool 


### <a name="step2" /> 2. Support for Transcription Conversion

**Festvox2MaryTranscripts** (optional)  
This Component converts Festvox Transcription format (ex: txt.done.data) into MARY format, that is one transcription file per sentence. 
Configuration Settings:  

    transcriptFile : Festvox format transcription file (Absolute path) 

The output of this component is a text directory in your voice building directory, containing one transcription file per sentence.  

### <a name="step3" /> 3. Automatic Labeling

**AllophonesExtractor**  
Creates the prompt_allophones directory and files required for HTK Labeling.

**HTKLabeler**  
HTK is a labeling tool, available for free from the University of Cambridge as of this publication, provider that the user registers himself or herself and agrees to the license. Info regarding this process can be found [here.](http://htk.eng.cam.ac.uk/) HTK is distributed in source code available for Linux/Unix and Windows operating systems. Once compiled, the HTKLabeler.htkDir setting needs to point to the directory where the HTK tool is located.

`htkDir : /your/path/to/htk/`

This labeler is used in lieu of the EHMM Labeler when building a Unit-Selection voice, as it is more time-efficient.  The output directory, under HTKLabeler settings HTKLabeler.outputLabDir should correspond with db.LabDir under Global Settings.

**LabelPauseDeleter**  
It is recommended to run the LabelPauseDeleter after the label files have been created by HTK, to avoid problems with subsequent voicebuilding components. 

**LabelledFilesInspector**  
It allows to browse through aligned labels and listen to the corresponding wave file. It is useful for perceptual manual verification on alignment. 


### <a name="step4" /> 4. Label-transcription Alignment  

**PhoneUnitLabelComputer**  
Converts the label files into the label files used by Mary: phone labels (phonelab directory)

**HalfPhoneUnitLabelComputer**  
Converts the label files into the label files used by Mary: halfphone labels (halfphonelab directory)

**TranscriptionAligner**  
This component will create the allophones directory.  

Note: all of these steps expect a folder /lab/ with all the label files that come from the HTK Labeler. The GUI will show green, as if the steps were completed successfully, even if the .lab files are not found. It is recommended to look in the Terminal for info on what files are being processed.

### <a name="step5" /> 5. Feature Vector Extraction from Text Data
**FeatureSelection**  
Allows you to select context features for building the voice.  `Note: This component requires the MARY server`  
The selected context feature names are saved in your voice building directory in: mary/features.txt

**PhoneUnitFeatureComputer**  
Computes Phone feature vectors for Unit Selection Voice building process. `Note: This component requires the MARY server`

You can connect to a different server by altering the settings. See the settings help for more information on this.  

**HalfPhoneUnitFeatureComputer**  
Computes half phone level feature vectors. 

### <a name="step6" /> 6. Verify Alignment  
**PhoneLabelFeatureAligner**  
It tries to align the labels and the feature vectors. If the alignment fails, you can start the automatic pause correction.  
This works as follows:  
- pauses, that are in the label file but not in the feature file are deleted in the label file, and the durations of the previous and next labels are stretched.  
- pauses that are in the feature file but not in the label file are inserted into the label file with length zero.  
If there are still errors after the pause correction, you are prompted for each error. You can skip the error or remove the corresponding file from the basename list (the list of files that are used for your voice). "skip all" and "remove all" does this for all problematic files. "Edit unit labels" allows you to edit the label file. "Edit RAWMARYXML" let you edit the maryxml that is the input for computing the features. You have to have a Maryserver running in order to recompute the features from the maryxml. You can alter the host and port settings for the server by altering the settings for the UnitFeatureComputer. 

**HalfPhoneLabelFeatureAligner**  
It works like the previous component.

### <a name="step7" /> 7. Basic Data Files  
**WaveTimelineMaker**  
The WaveTimelineMaker splits the waveforms as datagrams to be stored in a timeline in Mary format. It produces a binary file, which contains all .wav files. 

**BasenameTimelineMaker**  
The BasenameTimelineMaker takes a database root directory and a list of basenames, and associates the basenames with absolute times in a timeline in Mary format. 

**MCepTimelineMaker**  
The MCepTimelineMaker takes a database root directory and a list of basenames, and converts the related wav files into a mcep timeline in Mary format. 

### <a name="step8" /> 8. Building acoustic models  
**PhoneUnitfileWriter**  
It produces a file containing all phone sized units. 

**PhoneFeatureFileWriter**  
It produces a file containing all the target cost features for the phone sized units. The module needs a file defining which features are to be used and what weights are given to them. They must be the same features as the ones that the PhoneFeatureComputer used. If you do not have a feature definition, the module tries to create one. 

**DurationCARTTrainer**  
It builds an acoustic model of durations in the database using the program "wagon" from the Edinburgh Speech tools.

**F0CARTTrainer**  
It builds acoustic models of F0 values like DurationCARTTrainer. It uses "wagon" and the files produced by PhoneUnitfileWriter and PhoneFeatureFileWriter. 

### <a name="step9" /> 9. Unit Selection Files  

**HalfPhoneUnitfileWriter**  
It produces a file containing all halfphone sized units.  

**HalfPhoneFeatureFileWriter**  
It produces a file containing all the target cost features for the phone sized units. The module needs a file defining which features are to be used and what weights are given to them. They must be the same features as the ones that the HalfPhoneFeatureComputer used. If you do not have a feature definition, the module tries to create one.  

**F0PolynomialFeatureFileWriter**
Similar to previous module, but for F0 polynomial features.

**AcousticFeatureFileWriter**  
It produces a file containing all the target cost features plus two acoustic target cost features for the halfphone sized units. Also produces a feature definition containing those features.

**JoinCostFileMaker**  
It produces a file containing all the join cost features for the halfphone sized units.  

**CARTBuilder**  
It builds a preselection tree for the target cost features using "wagon" (CART) from the Edinburgh Speech tools.    
Additionally, the user needs to specify either a feature sequence or a top level tree. They are used to built a basic tree that is extended by wagon. This way, wagon runs several times on smaller subsets of units rather than the whole set. It might still take some time to run this module.

    Feature sequence: A file containing a list of features for which to build the tree.
    Top level tree: A file containing the basic tree. 

If you give the CARTBuilder neither a feature sequence nor a top level tree file, a default feature sequence is created which only contains "mary_phoneme" as feature. If the basic tree contains leaves that contain more units than the maximum number of units allowed, the leaves are pruned and a warning message is printed. It is recommended that you make sure that there are no leaves that are too big.  


### <a name="step10" /> 10. Adding a new unit selection voice in MARY 
**VoiceCompiler**  
Compiles the voice to be used in MARY TTS. The default setting values of this component are already fixed.  
Once the voice is compiled, follow the instructions in [Publishing-a-MARY-TTS-Voice](https://github.com/marytts/marytts/wiki/Publishing-a-MARY-TTS-Voice) to install the voice.