# Working on MaryTTS 6.X in Eclipse #

These explanations are, for now, **only available** for the [design\_data\_types](https://github.com/seblemaguer/marytts/tree/design_data_types) branch

## Preliminaries ##

The easiest and therefore recommended way to debug or develop MaryTTS is using [Eclipse IDE for Java developers](http://eclipse.org).
We have tested with Eclipse **Mars**.
Older versions will **probably not** work as advertised.

To use MaryTTS with eclipse, it is necessary to install few plugins : [gradle (STS)](https://marketplace.eclipse.org/content/gradle-sts-integration-eclipse), [Groovy-Eclipse](https://github.com/groovy/groovy-eclipse/wiki) (Groovy Integration) (which is also available as part of [GGTS](https://marketplace.eclipse.org/content/groovygrails-tool-suite-ggts-eclipse)) and [EGit](https://marketplace.eclipse.org/content/egit-git-team-provider). This last one is only necessary for a direct integration with Git.

You can use the drag and drop facility process proposed on the previous pages or they are available throught the marketplace (**Help** > **Eclipse Marketplace...**).

## Importing MaryTTS ##

### Alternative: Clone, then Import ###

Sometimes it just works better to have independent control over the cloning process.
You could use any tool you want to clone the MaryTTS source code repository from GitHub.

If you prefer to do it in Eclipse, here's how:

#### Clone with EGit ####

**THIS SECTION IS NOT VALID FOR NOW**

1. Choose **File** > **Import...**

2. Expand **Git** and select **Projects from Git**, then click **Next**

3. Select **Clone URI**, then click **Next**

4. Enter the URI `https://github.com/marytts/marytts.git`, then click **Next**

5. Select the branch `master`, then click **Next**

6. Choose a local directory to contain your new cloned repository, then click **Next**

7. Select **Use the New Project wizard**, then click **Finish**

8. Cancel the following dialog

After the git repository has been cloned,

#### Import with gradle (STS) ####

1. Choose **File** > **Import...**
2. Expand **Gradle (STS)** and select **Gradle (STS) Project**, then click **Next**
3. Choose the local directory containing your new cloned repository, then click **Build Model**
4. Click **Select All**, however you should deselect *marytts-builder* and *marytts-transcription*
5. Finally click on finish

## Running and/or debugging the MaryTTS Server ##


1. Choose **Run** > **Run Configurations...** (or **Run** > **Debug Configurations**)

2. Select **Java Application** and click the little **New** button

3. Set the **Name** to "Mary"

4. In the **Main** tab, set the **Project** to `marytts`

5. Set the **Main class** to `marytts.server.Mary`

6. In the **Arguments** tab, enter the following into the **VM arguments** field:

		-ea -Xmx1g -Dlog4j.logger.marytts=DEBUG,stderr -Dmary.base="${workspace_loc:marytts}/build"

7. In the **Classpath** tab, select **User Entries**, then click **Add Projects...** and select all the projects, then click **OK**.

   If you would like to add some other *installed* voice (see [below](#running-or-debugging-the-voice-installer)), click **Add JARs...** and select the corresponding voice jar file. If that voice has another locale than English, be sure to also click **Add Projects...** and select the corresponding language component.
   E.g., to debug MaryTTS with the German `bits1` HSMM voice (version 5.1.2) *installed*, add the `voice-bits1-hsmm-5.1.2.jar` JAR file and the `marytts-lang-de` project.

8. Click **Apply**, then click **Run** (or **Debug**)

Navigate to http://localhost:59125/ in your web browser and as you interact with the MaryTTS server, note the `DEBUG` level output in your Eclipse console.
