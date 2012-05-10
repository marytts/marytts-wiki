This page describes how to make available a synthesis voice for use with MARY TTS 5.x.

1. Build a voice using the voice import toolkit. As a result, you should find, in your voice's maven directory, an installable package `voice-...zip` and an XML file `voice-...-component.xml`.

2. Edit the XML such that it describes your voice properly. Pay special attention to the license terms, and make sure that the license html page can be displayed in the marytts-component-installer.

3. Test the installation as follows. After building the runtime MARY TTS system (using `mvn install` as described in the README), copy your `voice-...zip` and `voice-...-component.xml` into the runtime system's download folder, e.g. `target/marytts-5.0-SNAPSHOT/download/`, and run the marytts component installer (in the example, `target/marytts-5.0-SNAPSHOT/bin/marytts-component-installer.sh`. Verify that the voice shows when selecting the respective language, and that it can be installed as expected.

4. Make the voice zip file available for download at a public URL. Free options include Google Drive (working only for voices of less than 25 MB, as of May 2012) and Dropbox. Take note of the public URL of the voice-zip package, and update the `<location>` tag in the XML accordingly.

5. In order to make the voice downloadable from the master copy of the MARY TTS system, you need to include your XML into the master copy of marytts-components.xml. To do that, fork the marytts repository on github, add the file to your local copy, and issue a pull request.

6. After a member of the MARY TTS team has merged your XML code into the master copy, users can immediately see your voice in their local installation of marytts -- all they need to do is click the "Update" link in the marytts component installer. 

7. The next release of MARY TTS will include your voice in the version of marytts-components.xml shipped with the package; so users don't even have to click "update" to see your voice.