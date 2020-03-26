IntelliJ IDEA JavaCard Project Template
=====================================

This is a template project for Java Card development using IntelliJ IDEA IDE.

Setup
=====================================

1.	Install latest 1.8.x Java SDK from [here](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html). (Java SE 8 Update xx JDK). Java 1.6 and lower are incompatible with current Ant version in IntelliJ. If using other versions, just ensure javac can emit *.class files in 1.2 version (-source and -target command line switches).
1.	Open project using IDEA’s `File` -> `Open project`
1.	Go to `Project` -> `Project Structure` -> `SDKs` and add SDK you installed on step 1. If there is already something, you can delete it before adding newly installed SDK
1. Download the Java Card Development Kit [here](https://www.oracle.com/java/technologies/java-archive-downloads-javame-downloads.html). Find the version of your Java Card and download that version's Development Kit. Note: Do not confuse this with the version of the Global Platform on your card.
1.	Go to `File` -> `Project Structure` -> `Libraries`. Remove current Classes entry. Then add from the Development Kit downloaded in the previous step `java_card_kit_2.x.x\lib\api.jar.
1.	Click on the `helloWorld`` package in the Project view and press Shift-F6 to rename it to the name you like. The same goes to `HelloWorld` applet.
1.	Open `Common.properties`` file and setup everything you need there according to comments, including path to Development Kit installation folder. Uncomment/comment entries, that control which card platform is the target.
1.	Open `build.xml` (this is an [`Ant`](http://ant.apache.org/) build package so you need `Ant` plugin to be installed in IDEA) and correct entries, that follow `${APPLETAIDPREFIX}` to suit your AID.
1.	To build applet, use [`Ant`](http://ant.apache.org/) window on the right of the screen. Use `binarize.all.standard` for Gemalto TOPs and similiar.
1.	Target files will be in the \out\helloWorld\javacard folder where helloWorld is your package name

If you have more than one applet in the project, edit build.xml and add a second entry under each <target> tag with the info you need.

Now you have build your applet using IDEA's menu '`Build` -> `Make project`.
