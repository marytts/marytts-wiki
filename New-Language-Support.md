# Adding support for a new language to MARY TTS
This page outlines the steps necessary to add support for a new language to MARY TTS 5.

The following picture outlines the overall process.

![New Language Workflow](NewLanguageWorkflow.png)

The following sections describe the various steps involved.

# 0. Set up your environment

## 0.1 Compile marytts builder tools

First make sure you have built the MARY TTS software from source, using `mvn install` as described in the README. All software required for building the voice should now be located below

    marytts/target/marytts-builder-<version>/

with all shell scripts used in this page located in 

    marytts/target/marytts-builder-<version>/bin/

So please either place that folder into your `$PATH` or prefix each call to a shell script with `marytts/target/marytts-builder-<version>/bin/`.

## 0.2 Create a wiki data directory

Create an empty directory as your wiki data directory.

In your wiki data directory run the `wkdb_setup.sh` with the appropriate parameters,

    wkdb_setup.sh [wkdb data path] [wkdb locale] [mary locale]

where

    wkdb data path: path for the wkdb data path a configuration file will be create
    wkdb locale: the two letter locale for your language (e.g.: en, de, te, it, ...)
    mary locale: the extended mary locale for your language (e.g.: en_US, en_GB, de, te, it, ...)

e.g.:

    wkdb_setup.sh ./ en en_US
    (i.e., marytts/target/marytts-builder-<VERSION>/bin/wkdb_setup.sh ./ en en_US)


This creates a config file `wkdb.conf` (Please look at the configuration file `wkdb.conf` and change it according to your needs)


# 1. Download xml dump of wikipedia in your language

  Information about where and how to download the wikipedia in several languages is in: http://en.wikipedia.org/wiki/Wikipedia_database

  for example:

* English xml dump of wikipedia available at : http://download.wikimedia.org/enwiki/latest/ ( example file: enwiki-latest-pages-articles.xml.bz2 4.1 GB )
* Telugu xml dump of wikipedia available at : http://download.wikimedia.org/tewiki/latest/

The following script should automate the download and unpacking of the wikipedia dump for your language.

    wkdb_download_wikidump.sh wkdb.conf 


## 2. Extract clean text and most frequent words

### 2.1. Split the xml dump

Once downloaded the best way to handle the xml dump is splitting it into small chunks. You can avoid this step if your wiki dump is not bigger than 500MB, and you do not have memory problems.

For example, after unziping the English wikipedia dump will be approx. 16GB, so for further processing it can be split using the Wikipedia Dump Splitter program, wrapped by the following script:
 
    wkdb_split_dump.sh wkdb.conf 


### 2.2. Wikipedia Markup cleaning and mysql database creation

The next step will be to extract clean text (without wikipedia markup) from the split xml files and save this text and a list of words in a mysql database.

First of all a mysql database should be created with all privileges. In ubuntu if you have mysql server installed a database can be created with:

    $mysql -u root -p
    Enter password: (ubuntu passwd in this machine)

    mysql> create database wiki;
    mysql> grant all privileges on wiki.* to mary@localhost identified by "wiki123";
    mysql> flush privileges;

Int this case the `wiki` database is created, all privileges are granted to user `mary` in the localhost and the password is for example `wiki123`.  These values will be used in the scripts below.

If you do not have rights for creating a mysql database, please contact your system administrator for creating one for you.

  Once you have a mysql database, you can start to extract clean text and words from the wikipedia split files using the `WikipediaProcessor` program.  The following script explains its usage and possible parameters (The scripts examples presented in this tutorial use the enwiki, that is locale en_US):


The wikilist.txt should contain something like:

    /current-dir/xml_splits/page1.xml
    /current-dir/xml_splits/page2.xml
    /current-dir/xml_splits/page3.xml
    ...

*NOTE:* If you experience memory problems you can try to split the big xml dump in smaller chunks.

*Output:*

- It creates a file `./done.txt` which contains the files already processed, in case the program stops it can be re-started and it will continue processing the not "done" files in the input list.

- A text file `./wordlist-freq.txt` containing the list of words and their frequencies, this file will be created after processing each xml file.

- It creates two tables in the the database, the name of the tables depends on the locale, for example if the locale is "en_US" it will create the tables `en_US_cleanText` and `en_US_wordList`, their description is:

        mysql> desc en_US_cleanText;
        +-----------+------------------+------+-----+---------+----------------+
        | Field     | Type             | Null | Key | Default | Extra          |
        +-----------+------------------+------+-----+---------+----------------+
        | id        | int(10) unsigned | NO   | PRI | NULL    | auto_increment |
        | cleanText | mediumblob       | NO   |     |         |                |
        | processed | tinyint(1)       | YES  |     | NULL    |                |
        | page_id   | int(10) unsigned | NO   |     |         |                |
        | text_id   | int(10) unsigned | NO   |     |         |                |
        +-----------+------------------+------+-----+---------+----------------+

        mysql> desc en_US_wordList;
        +-----------+------------------+------+-----+---------+----------------+
        | Field     | Type             | Null | Key | Default | Extra          |
        +-----------+------------------+------+-----+---------+----------------+
        | id        | int(11)          | NO   | PRI | NULL    | auto_increment |
        | word      | tinyblob         | NO   |     |         |                |
        | frequency | int(10) unsigned | NO   |     |         |                |
        +-----------+------------------+------+-----+---------+----------------+


## 3. Transcribe most frequent words

  Transcribe most frequent words using MARY Transcription Tool. Transcription Tool is a graphical user interface which supports a semi-automatic procedure for transcribing new language text corpus and automatic training of Letter-to-sound(LTS) rules for that language. It stores all functional words in that language to build a primitive POS tagger.

  Create pronunciation dictionary,  train letter-to-sound rules and prepare list of functional words for primitive POS tagger using MARY Transcription Tool.

  More details available at  http://mary.opendfki.de/wiki/TranscriptionTool

## 4. Minimal NLP components for the new language

With the files generated by the Transcription tool, we can now create a first instance of the NLP components in the TTS system for our language.

We add support for our language to MARY TTS by creating a new config file in the folder MARY TTS\conf. By convention the file is called <locale>.config. It tells the MARY server which TTS modules to load, and which data files to use.

The following is an example for Turkish (locale "tr").

{{{
##########################################################################
# MARY TTS configuration file tr.config
##########################################################################

name = tr
tr.version = 4.0.0

provides = a-language

requires = \
    marybase


###########################################################################
############################## The Modules  ###############################
###########################################################################
modules.classes.list = \
        marytts.modules.JPhonemiser(tr.)  \
        marytts.modules.MinimalisticPosTagger(tr,tr.) \


####################################################################
####################### Module settings  ###########################
####################################################################

# Phonemiser settings
tr.allophoneset = MARY_BASE/lib/modules/tr/lexicon/allophones.tr.xml
tr.lexicon = MARY_BASE/lib/modules/tr/lexicon/tr_lexicon.fst
tr.lettertosound = MARY_BASE/lib/modules/tr/lexicon/tr.lts
#tr.userdict = MARY_BASE/lib/modules/tr/lexicon/userdict.txt

# POS tagger settings
tr.partsofspeech.fst = MARY_BASE/lib/modules/tr/tagger/tr_pos.fst
tr.partsofspeech.punctuation = ,.?!;

}}}
It can be seen that the tr.config file refers to the following files:

{{{
MARY_BASE/lib/modules/tr/lexicon/allophones.tr.xml
MARY_BASE/lib/modules/tr/lexicon/tr_lexicon.fst
MARY_BASE/lib/modules/tr/lexicon/tr.lts
MARY_BASE/lib/modules/tr/tagger/tr_pos.fst
}}}
They must be copied from the TranscriptionGUI folder to the expected place on the file system.

Now, it should be possible to start the mary server, and place a query via the HTTP interface, for input format TEXT, locale tr, and output formats up to TARGETFEATURES. A suitable test request can be placed from http://localhost:59125/documentation.html. It is a good idea to check whether the output for TOKENS, PARTSOFSPEECH, PHONEMES, INTONATION and ALLOPHONES looks roughly as expected.

In order to continue with the next step, you will need to have a mary server with this config file running, so that the FeatureMaker can compute feature vectors for computing diphone coverage.

## 5. Run feature maker with the minimal nlp components

The '''FeatureMaker''' program splits the clean text obtained in step 2 into sentences, classify them as reliable, or non-reliable (sentences with unknownWords or strangeSymbols) and extracts context features from the reliable sentences. All this extracted data will be  kept in the DB.


There is a variant of the program, '''FeatureMakerMaryServer''', which calls an external Mary server instead of starting the Mary components internally. It takes the additional command line arguments ''-maryHost localhost -maryPort 59125''.

Output:

- After processing every cleanText record it will mark the record as processed=true, so if the program stops it can be re-started and it will continue processing the non-processed cleanText records.

- A file containing the feature definition of the features used for selection, the name of this file depends on the locale, for example for "en_US" it will be "/current-dir/en_US_featureDefinition.txt". This file will be used in the Database selection step.

- It creates one table in the the database, the name of the table depends on the locale, for example if the locale is "en_US" it will create the table en_US_dbselection, its descriptions is:

{{{
mysql> desc en_US_dbselection;
+----------------+------------------+------+-----+---------+----------------+
| Field          | Type             | Null | Key | Default | Extra          |
+----------------+------------------+------+-----+---------+----------------+
| id             | int(11)          | NO   | PRI | NULL    | auto_increment | 
| sentence       | mediumblob       | NO   |     |         |                | 
| features       | blob             | YES  |     | NULL    |                | 
| reliable       | tinyint(1)       | YES  |     | NULL    |                | 
| unknownWords   | tinyint(1)       | YES  |     | NULL    |                | 
| strangeSymbols | tinyint(1)       | YES  |     | NULL    |                | 
| selected       | tinyint(1)       | YES  |     | NULL    |                | 
| unwanted       | tinyint(1)       | YES  |     | NULL    |                | 
| cleanText_id   | int(10) unsigned | NO   |     |         |                | 
+----------------+------------------+------+-----+---------+----------------+
}}}

## 6. Database selection

The '''DatabaseSelector''' program selects a phonetically/prosodically balanced recording script.

The following script explains its usage and possible parameters:


'''Output:'''

- Several log information in "/current-dir/selection/" directory

- A file containing the selected sentences in "/current-dir/selected.log"

- The id's of the selected sentences are marked as selected=true in dbselection

- It creates a locale_***_selectedSentences table in the the database. The name of the table depends on the locale, and the name provided by the user with the option -tableName,  for example if the user provided -tableName "Test" and the locale is "en_US" it will create the table:

{{{
mysql> desc en_US_Test_selectedSentences;
+----------------+------------------+------+-----+---------+----------------+
| Field          | Type             | Null | Key | Default | Extra          |
+----------------+------------------+------+-----+---------+----------------+
| id             | int(11)          | NO   | PRI | NULL    | auto_increment | 
| sentence       | mediumblob       | NO   |     |         |                | 
| unwanted       | tinyint(1)       | YES  |     | NULL    |                | 
| dbselection_id | int(10) unsigned | NO   |     |         |                | 
+----------------+------------------+------+-----+---------+----------------+
}}}

Also a description of this table will be set in the tablesDescription table.

The tablesDescription has information about:

{{{
mysql> desc tablesDescription;
+----------------------------+------------+------+-----+---------+----------------+
| Field                      | Type       | Null | Key | Default | Extra          |
+----------------------------+------------+------+-----+---------+----------------+
| id                         | int(11)    | NO   | PRI | NULL    | auto_increment | 
| name                       | tinytext   | YES  |     | NULL    |                | 
| description                | mediumtext | YES  |     | NULL    |                | 
| stopCriterion              | tinytext   | YES  |     | NULL    |                | 
| featuresDefinitionFileName | tinytext   | YES  |     | NULL    |                | 
| featuresDefinitionFile     | mediumtext | YES  |     | NULL    |                | 
| covDefConfigFileName       | tinytext   | YES  |     | NULL    |                | 
| covDefConfigFile           | mediumtext | YES  |     | NULL    |                | 
+----------------------------+------------+------+-----+---------+----------------+
}}}

## 7. Manually check/correct transcription of all words in the recording script [Optional]

The '''SynthesisScriptGUI''' program allows you to check the sentences selected in the previous step, discard some (or all) and select and add more sentences.

The following script can be used to start the GUI:

{{{
#!/bin/bash

export MARY_BASE="[PATH TO MARY BASE]"
export CLASSPATH="$MARY_BASE/java/"

java -classpath $CLASSPATH marytts.tools.dbselection.SynthesisScriptGUI

}}}

[[Image(synthesisScriptGUI.png)]]


Synthesis script menu options:

1. '''Run DatabaseSelector''': Creates a new selection table or adds sentences to an already existing one.

 * After running the DatabaseSelector the selected sentences are loaded.

2. '''Load selected sentences table''': reads mysql parameters and load a selected sentences table.

 * Once the sentences are loaded, use the checkboxes to mark sentences as unwanted/wanted.
 * Sentences marked as unwanted can be unselected and set as wanted again.
 * The DB is updated every time a checkbox is selected.
 * There is no need to save changes. Changes can be made before the window is updated or the program exits.

3. '''Save synthesis script as''': saves the selected sentences, without unwanted, in a file.

4. '''Print table properties''': prints the properties used to generate the list of sentences.

5. '''Update window''': presents the table without the sentences marked as unwanted.

6. '''Help''': presents this description.

7. '''Exit''': terminates the program.

## 8. Record script with a native speaker using our recording tool "Redstart"

In the recording tool Redstart, there is an import functionality for the text files generated from the synthesis script selection GUI. From the Redstart menu, select "File"->"Import text file..." and follow the on-screen instructions.

More information: [http://mary.opendfki.de/wiki/RedStart RedStart: Voice recording tool for TTS]

## 9. Convert recorded audio
Usually it makes sense to convert the audio recorded from the speaker before building a synthetic voice from it. MARY provides a GUI that provides a range or processing options. It can be started as follows:

{{{
java -cp mary-common.jar:swing-layout-1.0.jar:signalproc.jar marytts.util.data.audio.AudioConverterGUI
}}}

[[Image(AudioConverterGUI.png)]]

The following options are provided:

 * Process only the best take of each sentence: Redstart saves various takes of the same sentence under names such as w0001.wav, w0001a.wav, w0001b.wav etc. If this option is selected, only the last recorded version, w0001.wav, will be processed.
 * Global amplitude scaling allows you to control the maximum amplitude of the converted files, independently of the recording amplitude. Power normalisation across recording sessions attempts to identify recording sessions by the time stamps of files: a pause longer than 10 minutes indicates a session break. For each session separately, a mean energy is computed, and conversion factors for each file are computed such that after the conversion, the average energy for all sessions is the same. The aim behind this processing is to compensate for the case that from one session to another, there may have been slightly different recording gains or minor differences in the speaker's distance to the microphone. Attention: This method can work only if the audio files have the original time stamps of the recordings, so take extra care when copying files if you intend to use this normalisation.
 * Stereo to mono conversion: If you recorded in stereo, you must convert to mono before building a voice. Choose either the left channel only, the right channel only, or a mix of both channels.
 * Remove low-frequency noise below 50 Hz: this applies a high-pass FIR filter with a cutoff frequency of 50 Hz and a transition bandwidth of 40 Hz. Since the FIR filter has a symmetric kernel, it has a linear phase response.
 * Trim initial and final silences: this applies a k-means clustering to identify silence vs. speech portions of the audio file, leaving 0.5 seconds initial and final silence. This is useful to avoid training absurdly long pause duration models.
 * If a sox binary is available, it is also possible to convert the sampling rate. A usual target rate is 16000 Hz, but other rates are also possible.

## 10. Build an unit selection and/or hmm-based voice with Voice import tools

See:

 * [wiki:VoiceImportToolsTutorial Building a New Voice for MARY using Voice Import Components]
 * [http://mary.opendfki.de/wiki/HMMVoiceCreation Creating an HMM-based voice]