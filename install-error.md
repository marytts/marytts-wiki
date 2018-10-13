for two days i'm trying to install just impossible marytts on ubuntu 12, 16, 18 no, can anyone help me

sudo -u mary mvn package


[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO]
[INFO] MaryTTS
[INFO] marytts-common
[INFO] marytts-signalproc
[INFO] marytts-runtime
[INFO] marytts-lang-de
[INFO] marytts-lang-en
[INFO] marytts-lang-te
[INFO] marytts-lang-tr
[INFO] marytts-lang-ru
[INFO] marytts-lang-it
[INFO] marytts-lang-fr
[INFO] marytts-lang-sv
[INFO] marytts-lang-lb
[INFO] marytts-languages
[INFO] voice-cmu-slt-hsmm
[INFO] marytts-client
[INFO] marytts-builder
[INFO] marytts-redstart
[INFO] marytts-transcription
[INFO] marytts-assembly-builder
[INFO] marytts-assembly-runtime
[INFO] marytts-assembly
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building MaryTTS 5.2
[INFO] ------------------------------------------------------------------------
Downloading: https://jcenter.bintray.com/org/apache/maven/plugins/maven-enforcer-plugin/1.4.1/maven-enforcer-plugin-1.4.1.pom
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: I/O exception (javax.net.ssl.SSLPeerUnverifiedException) caught when processing request: peer not authenticated
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: Retrying request
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: I/O exception (javax.net.ssl.SSLPeerUnverifiedException) caught when processing request: peer not authenticated
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: Retrying request
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: I/O exception (javax.net.ssl.SSLPeerUnverifiedException) caught when processing request: peer not authenticated
Oct 13, 2018 6:43:34 AM org.apache.commons.httpclient.HttpMethodDirector executeWithRetry
INFO: Retrying request
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] MaryTTS ........................................... FAILURE [0.543s]
[INFO] marytts-common .................................... SKIPPED
[INFO] marytts-signalproc ................................ SKIPPED
[INFO] marytts-runtime ................................... SKIPPED
[INFO] marytts-lang-de ................................... SKIPPED
[INFO] marytts-lang-en ................................... SKIPPED
[INFO] marytts-lang-te ................................... SKIPPED
[INFO] marytts-lang-tr ................................... SKIPPED
[INFO] marytts-lang-ru ................................... SKIPPED
[INFO] marytts-lang-it ................................... SKIPPED
[INFO] marytts-lang-fr ................................... SKIPPED
[INFO] marytts-lang-sv ................................... SKIPPED
[INFO] marytts-lang-lb ................................... SKIPPED
[INFO] marytts-languages ................................. SKIPPED
[INFO] voice-cmu-slt-hsmm ................................ SKIPPED
[INFO] marytts-client .................................... SKIPPED
[INFO] marytts-builder ................................... SKIPPED
[INFO] marytts-redstart .................................. SKIPPED
[INFO] marytts-transcription ............................. SKIPPED
[INFO] marytts-assembly-builder .......................... SKIPPED
[INFO] marytts-assembly-runtime .......................... SKIPPED
[INFO] marytts-assembly .................................. SKIPPED
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.411s
[INFO] Finished at: Sat Oct 13 06:43:34 UTC 2018
[INFO] Final Memory: 10M/111M
[INFO] ------------------------------------------------------------------------
[ERROR] Plugin org.apache.maven.plugins:maven-enforcer-plugin:1.4.1 or one of its dependencies could not be resolved: Failed to read artifact descriptor for org.apache.maven.plugins:maven-enforcer-plugin:jar:1.4.1: Could not transfer artifact org.apache.maven.plugins:maven-enforcer-plugin:pom:1.4.1 from/to central (https://jcenter.bintray.com): peer not authenticated -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/PluginResolutionException
root@vps-2787-nextwab:/local/mary/marytts#
