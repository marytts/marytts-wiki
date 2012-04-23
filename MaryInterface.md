New in MARY TTS 5 is a unified API for accessing both local and remote synthesis:

    marytts.MaryInterface


This is intended as an easy-to-use access API for using MARY TTS.

The same API can be used to interact with a local TTS runtime, in the same JVM, or with a remote TTS server via a client-server protocol.

The basic idea is to use reasonable defaults when instantiating a MaryInterface, but to let the user adapt all parts of it.

## Examples of use

### Simplest text-to-speech

    MaryInterface marytts = new LocalMaryInterface();
    AudioInputStream audio = marytts.generateAudio("This is my text.");

### Default voice in a different language

    MaryInterface marytts = new LocalMaryInterface();
    marytts.setLocale(Locale.SWEDISH);
    AudioInputStream audio = marytts.generateAudio("Välkommen till talsyntesens värld!");

### Custom voice

    MaryInterface marytts = new LocalMaryInterface();
    marytts.setVoice("dfki-pavoque-neutral"); // a German voice
    AudioInputStream audio = marytts.generateAudio("Hallo und willkommen!");

### Other input and output types

(for advanced users; you need to know what you are doing to use this meaningfully)

    MaryInterface marytts = new LocalMaryInterface();
    marytts.setInputType("SSML");
    marytts.setOutputType("TARGETFEATURES");
    marytts.setLocale(Locale.SWEDISH);
    Document ssmlDoc = DomUtils.parseDocument("myfile.ssml");
    String targetfeatures = marytts.generateText(ssmlDoc);


### Remote (client / server) TTS

The exact same syntax should work with the RemoteMaryInterface included in the marytts-client package:

    MaryInterface marytts = new RemoteMaryInterface("localhost", 59125);
    AudioInputStream audio = marytts.generateAudio("This is my text.");

### Some introspection

    MaryInterface marytts = new LocalMaryInterface();
    System.out.println("I currently have " + marytts.getAvailableVoices() + " voices in "
        + marytts.getAvailableLocales() + " languages available.");
    System.out.println("Out of these, " + marytts.getAvailableVoices(Locale.US) + " are for US English.");

