IntelliJ IDEA JavaCard Project Template
=====================================

This is a template project for Java Card development using [IntelliJ IDEA IDE](https://www.jetbrains.com/idea/).
This tutorial is optimised for working with cards handed out by the Radboud University Nijmegen for the course 
Hardware Security. Cards with similar properties can also be used, like [this](https://www.smartcardfocus.com/shop/ilp/id~712/javacos-a22-dual-interface-java-card-150k/p/index.shtml)
one.

Setup
=====================================

1.	Install latest 1.8.x Java SDK from [here](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html).
 (Java SE 8 Update xx JDK). Java 1.6 and lower are incompatible with current Ant version in IntelliJ. If using other 
 versions, just ensure javac can emit *.class files in 1.2 version (-source and -target command line switches). 
  Java SE 14 seems to have a problem with emitting these files. 
1.	Open project using IDEA’s `File` -> `Open project`
1.	Go to `Project` -> `Project Structure` -> `SDKs` and add SDK you installed on step 1. If there is already something, 
you can delete it before adding newly installed SDK.
1.  Go to `Project` -> `Project Structure` -> `Project` and again select the SDK you installed on step 1. Set the
`project language level` to `SDK Default` 
1.  Download the Java Card Development Kit [here](https://www.oracle.com/java/technologies/java-archive-downloads-javame-downloads.html).
 Find the version of your Java Card and download that version's Development Kit. 2.2.1 in the case for RU cards, 2.2.2
 for the card from smartcardfocus. <br>
 Note: Do not confuse the Java Card version with the version of the Global Platform on your card.
1.	Go to `File` -> `Project Structure` -> `Libraries`. Remove current Classes entry. Then add from the Development Kit
 downloaded in the previous step `java_card_kit_2.x.x\lib\api.jar`.
1.	Click on the `helloWorld` package in the Project view and press Shift-F6 to rename it to the name you like. The 
same goes to `HelloWorld` applet.
1.	Open `Common.properties` file and setup everything you need there according to comments, including path to Java Card
Development Kit installation folder. Uncomment/comment entries, that control which card platform is the target.
1.	Open `build.xml` (this is an [`Ant`](http://ant.apache.org/) build package so you need `Ant` plugin to be installed
 in IDEA) and correct entries, that follow `${APPLETAIDPREFIX}` to suit your AID.
1.  Enable the Ant view through `View` -> `Tool Windows` -> `Ant`.
1.  Add the build file to [`Ant`](http://ant.apache.org/)  by clicking the `+` sign in the Ant view, navigate to your
 project and select `build.xml`.
1.	To build applet, use [`Ant`](http://ant.apache.org/) window on the right of the screen and double click
 `binarize.all.standard`.
1.	Target files will be in the `\out\helloWorld\javacard` folder where `helloWorld` is your package name

If you have more than one applet in the project, edit build.xml and add a second entry under each <target> tag with the 
info you need.

# Interfacing with the Card

_**Important: Default keys are assumed when interfacing with the cards. Make sure that your card uses the default keys 
 before proceeding. Communicating with the wrong keys might brick the card.**_

Personally I'm using the [ACR122](https://www.smartcardfocus.com/shop/ilp/id~242/acr122u/p/index.shtml) to communicate
with the card. A nifty tool for managing the card and custom APDU commands is 
[PyApduTool](https://www.javacardos.com/tools).

## Managing cards with PyApduTool
Run the tool PyApduTool.exe directly (no need to run from the JCIDE). Go to the first tab `Reader`. From here select 
your reader. Place the card on (or in) the reader and hit the `Connect` button. In the box to the right some text should
appear, like the type of the reader, the protocol it is using and some value for ATR.

### Installing a new applet
1. Go to tab `GlobalPlatform` and to its sub-tab `Manager`. Hit the button `Cap File...` and select your .cap file.
This can be found in the target file folder (`\out\helloWorld\javacard` folder where `helloWorld` is your package name).
1. Hit the button `Download` to download your .cap file to the card. The box labelled `Information` at the bottom 
should say 
```
Download Cap begin... 
Download Cap successful. 
```
1. Hit the button `Install` to install the applet. Again the `Information` box should say `install begin` and 
`install successful`.
1. Hit the button `Select` to make the freshly installed applet the active one. Again the `Information` box
 should say `select begin` and `select successful`.
1. Go to the tab `Apdu`. Select the round before `CLA` and in that row fill in 30 for `CLA`, 30 for `INS`, 0 for `P1`, 
0 for `P2` and leave `LC` and `LE` empty.
1. Select the tab `Basic APDU` at the bottom.
1. In the box `Data` below `CLA` fill in some hexadecimal string and hit the button `Send`. In the case that the box
`Data` contains `AA BB CC DD` then in the box `Basic APDU` it will say
```
<<30 30 00 00 04 AA BB CC DD
>>AA BB CC DD 90 00
```
This is the intended behaviour of the HelloWorld applet. So this output indicates that everything went right so far.

### Removing an applet
1. Go to tab `GlobalPlatform` and sub-tab `Package`. Hit `Refresh`
1. Select the package you want to remove and hit `Remove` or `Remove All`.



