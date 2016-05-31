# Working on MaryTTS 5.2 in Eclipse #

## Preliminaries ##

The easiest and therefore recommended way to debug or develop MaryTTS is using [Eclipse IDE for Java developers](http://eclipse.org).
We have tested with Eclipse Juno, Kepler, Luna, and Mars.
Older versions will probably *not* work as advertised.

Two required Eclipse plugins are [m2e](https://marketplace.eclipse.org/content/maven-integration-eclipse-luna-and-newer) (Maven Integration) and [Groovy-Eclipse](https://github.com/groovy/groovy-eclipse/wiki) (Groovy Integration) (which is also available as part of [GGTS](https://marketplace.eclipse.org/content/groovygrails-tool-suite-ggts-eclipse));
depending on which variant of Eclipse you are using, they are either already installed or available for installation in the Eclipse Marketplace (**Help** > **Eclipse Marketplace...**).

For direct integration with Git, you'll also need [EGit](https://marketplace.eclipse.org/content/egit-git-team-provider) (Team provider for Git), and to clone and import in one step (see below), you will also need the **Maven SCM Handler for EGit**.
Again, depending on you Eclipse installation, you may need to install one or both of these manually.

## Importing MaryTTS ##

You can *either* import MaryTTS directly in one step *or* clone the Git repository and then import the Maven project.

### One-step import ###

The easiest way to import MaryTTS into your Eclipse workspace is to clone and import in one step (using the plugins detailed above).

1. Choose **File** > **Import...**

2. Expand **Maven** and select **Checkout Maven Projects from SCM**, then click **Next**

3. Select **git** and enter the **SCM URL** `https://github.com/marytts/marytts.git`, then click **Finish**

### Alternative: Clone, then Import ###

Sometimes it just works better to have independent control over the cloning process.
You could use any tool you want to clone the MaryTTS source code repository from GitHub.

If you prefer to do it in Eclipse, here's how:

#### Clone with EGit ####

1. Choose **File** > **Import...**

2. Expand **Git** and select **Projects from Git**, then click **Next**

3. Select **Clone URI**, then click **Next**

4. Enter the URI `https://github.com/marytts/marytts.git`, then click **Next**

5. Select the branch `master`, then click **Next**

6. Choose a local directory to contain your new cloned repository, then click **Next**

7. Select **Use the New Project wizard**, then click **Finish**

8. Cancel the following dialog

After the git repository has been cloned,

#### Import with m2e ####

1. Choose **File** > **Import...**

2. Expand **Maven** and select **Existing Maven projects**, then click **Next**

3. Choose the local directory containing your new cloned repository, then click **Finish**

## Assemble and install MaryTTS ##

Make the MaryTTS assemblies and install the Maven artifacts, which are required to install or build new voices.
On the command line, you could simply run `mvn install`.
Alternatively, using Eclipse, in the Package Explorer, right-click on the `marytts` project (at the top), then select **Run As** > **Maven install**

You now have the MaryTTS Runtime and Builder (for new voices and languages) assemblies under the `target` directory of your Git repository, and the MaryTTS Maven artifacts installed in your local Maven repository.

## Running (or Debugging) the MaryTTS Server ##

1. Choose **Run** > **Run Configurations...** (or **Run** > **Debug Configurations**)

2. Select **Java Application** and click the little **New** button

3. Set the **Name** to "Mary"

4. In the **Main** tab, set the **Project** to `marytts-runtime`

5. Set the **Main class** to `marytts.server.Mary`

6. In the **Arguments** tab, enter the following into the **VM arguments** field:

		-ea -Xmx1g -Dlog4j.logger.marytts=DEBUG,stderr -Dmary.base="${workspace_loc:marytts}/target/marytts-VERSION"

	where `VERSION` is the current version in your MaryTTS `pom.xml`.
	This may very well be something suffixed with `-SNAPSHOT`.

7. In the **Classpath** tab, select **User Entries**, then click **Add Projects...** and
   select `marytts-lang-en` and `voice-cmu-slt-hsmm`, then click **OK**.

   If you would like to add some other *installed* voice (see [below](#running-or-debugging-the-voice-installer)), click **Add JARs...** and select the corresponding voice jar file. If that voice has another locale than English, be sure to also click **Add Projects...** and select the corresponding language component.
   E.g., to debug MaryTTS with the German `bits1` HSMM voice (version 5.1.2) *installed*, add the `voice-bits1-hsmm-5.1.2.jar` JAR file and the `marytts-lang-de` project.

8. Click **Apply**, then click **Run** (or **Debug**)

Navigate to http://localhost:59125/ in your web browser and as you interact with the MaryTTS server, note the `DEBUG` level output in your Eclipse console.

## Running (or Debugging) the Voice Installer ##

1. Choose **Run** > **Run Configurations...** (or **Run** > **Debug Configurations**)

2. Select **Java Application** and click the little **New** button

3. Set the **Name** to "InstallerGUI"

4. In the **Main** tab, set the **Project** to `marytts-runtime`

5. Set the **Main class** to `marytts.tools.install.InstallerGUI`

6. In the **Arguments** tab, enter the following into the **VM arguments** field:

		-Dmary.base="${workspace_loc:marytts}/target/marytts-VERSION"

	where `VERSION` is the current version in your MaryTTS `pom.xml`.
	This may very well be something suffixed with `-SNAPSHOT`.

8. Click **Apply**, then click **Run** (or **Debug**)

## Running (or Debugging) the Voicebuilding Tools ##

Assuming you have a new voicebuilding directory containing

1. a `wav` subdirectory with recorded utterances, one per file, and

2. a `text` subdirectory with corresponding `.txt` files of those utterances, one line per file,

you can build a new voice from within Eclipse in the following way:

1. Choose **Run** > **Run Configurations...** (or **Run** > **Debug Configurations**)

2. Select **Java Application** and click the little **New** button

3. Set the **Name** to "DatabaseImportMain"

4. In the **Main** tab, set the **Project** to `marytts-builder`

5. Set the **Main class** to `marytts.tools.voiceimport.DatabaseImportMain`

6. In the **Arguments** tab, enter the following into the **VM arguments** field:

		-ea -Xmx1g -Duser.dir=MYVOICEDIR

	where `MYVOICEDIR` is the voicebuilding directory containing your voice data.

7. In the **Classpath** tab, select **User Entries**, then click **Add Projects...** and
   select `marytts-lang-en` (or if you want to build a voice for a different language, the Maven module project corresponding to that locale), then click **OK**

8. Click **Apply**, then click **Run** (or **Debug**)
