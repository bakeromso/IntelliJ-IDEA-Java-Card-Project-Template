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

## Managing cards with PyApduTool (GUI)
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
### Communicating with the applet
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

## Managing cards from commandline (windows)
Get the latest version of GlobalPlatformPro from [here](https://github.com/martinpaljak/GlobalPlatformPro/releases).
I decided to go with the gp.jar version. 
1. Open a command prompt and set a variable for your .cap output directory. <br>
`set OUTPUT_DIR=C:\Users\user\workspace\project\out\helloWorld\javacard`
1. Navigate to the location where you placed the gp.jar file.
### Listing installed packages and applets
Execute the following to list installed packages and applets:
```
java -jar gp.jar -list
```
For me this gave the following output.
```
Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
ISD: A000000003000000 (OP_READY)
     Privs:   SecurityDomain, CardLock, CardTerminate, CardReset, CVMManagement
```
OP_READY means the card is in development mode, which is what we want for now. No packages nor any applets are 
listed which means the card is empty. Let's proceed by installing an applet.
### Install an applet with default privileges
Execute the following to install helloWorld.cap with default privileges. Replace helloWorld.cap with your .cap 
name if yours is named differently.
```
java -jar gp.jar -default -install %OUTPUT_DIR%\helloWorld.cap
```
Which gives the output
```
Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
CAP loaded
```
Let's check the installed applet by listing the card's contents
```
java -jar gp.jar -list
```
Now giving the output:
```
Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
ISD: A000000003000000 (OP_READY)
     Privs:   SecurityDomain, CardLock, CardTerminate, CardReset, CVMManagement

APP: DEADBEEF31321001 (SELECTABLE)
     Privs:   CardReset

PKG: DEADBEEF313210 (LOADED)
     Version: 1.0
     Applet:  DEADBEEF31321001
```
The package `DEADBEEF313210` is installed with the applet `DEADBEEF31321001`.
### Removing an applet
Applets can be removed in multiple ways. The easiest way is by using `-uninstall` from GlobalPlatformPro. This
looks as follows.
```
java -jar gp.jar -default -uninstall %OUTPUT_DIR%\helloWorld.cap
```
The package AID and applet AID are taken from the specified .cap file. If you want to do this manually, first 
list the contents of the card:
```
java -jar gp.jar -list
```
Then note the listed package AID (in my case `DEADBEEF313210`). Delete it by executing the following line, where 
you should replace `DEADBEEF313210` with your package AID. This will delete the package and all its applets from
the card.
```
java -jar gp.jar -delete DEADBEEF313210          
```
### Communicating with the applet
Through GlobalPlatformPro it is possible to send raw APDU commands so that we can talk with the applet. However 
I did not manage to get this to work with GlobalPlatformPro, which is why I used [apdu4j](https://github.com/martinpaljak/apdu4j/releases)
for this.
#### Communicating through apdu4j
Navigate to the location where you placed the apdu4j.jar file. <br>
Find the name of your reader
 ```
java -jar apdu4j.jar -l
```
Which in my case gave the following output.
 ```
[*] ACS ACR122U PICC Interface 0
[ ] JAVACOS Virtual Contact Reader 0
[ ] JAVACOS Virtual Contactless Reader 1
 ```
Copy the exact name of the reader you would like to use, we will use this to specify which reader to use.
<br>
In order to select the applet with any specific reader execute the following. Replace the part after 
`-r` with the name of your reader noted in the previous step. 
 ```
java -jar apdu4j.jar -r "ACS ACR122U PICC Interface 0" apdu 00a4040008DEADBEEF3132100100
 ```
The value after `-a` is copied from PyApduTool. If your package AID is not `DEADBEEF31321001` then I 
believe you need to change this APDU command as follows. `00a40400 + number of bytes in your package
AID + your package AID + 00`.
Now finally in order to communicate with the applet itself we will send some data to the helloWorld 
applet. Add the `-d` flag to see the reply of the card.
```
java -jar apdu4j.jar -r "ACS ACR122U PICC Interface 0" -d apdu 3030000004aabbccdd
```
This gives the following output:
```
# Using ACS ACR122U PICC Interface 0
SCardConnect("ACS ACR122U PICC Interface 0", EXCLUSIVE;*) -> T=1, 3B8F800100664653051000FF71DF000000000039
A>> T=1 (4+0004) 30300000 04 AABBCCDD
A<< (0004+2) (20ms) AABBCCDD 9000
SCardDisconnect("ACS ACR122U PICC Interface 0", true) tx:9/rx:6
```
The reply (line with `A<<`) shows that the card replied with the same payload of `AABBCCDD`. This is
the intended behaviour of the helloWorld application. The contents of the payload can be altered, but
make sure that the `04` in `3030000004aabbccdd` indicates the size of the payload (number of bytes
after `04`, in this case `aabbccdd`).<br>
<br>
APDU commands can also be chained like this
``` 
java -jar apdu4j.jar -r "ACS ACR122U PICC Interface 0" -d apdu 00a4040008DEADBEEF3132100100 3030000004aabbccdd
```

#### More info on the attempt in GlobalPlatformPro
Through GlobalPlatformPro it would be possible to send raw commands with the `-s` flag. Add the `-d` flag to see
the actually transmitted commands. `A>>>` indicates what was send to the card and `A<<<` the card's reply.
```
java -jar gp.jar -s 00A4040008DEADBEEF3132100100
java -jar gp.jar -d -s 3030300004AABBCCDD
```
The first command is to select the applet on the card, the second one is to send the data `AABBCCDD` to the card. 
These commands are copied from the `Basic APDU` box in the PyApduTool when selecting an applet and sending 
`AABBCCDD` to the card.
The last part of the reply is as follows.
```
A>> T=1 (4+0012) 34303000 0C AABBAAAA590585631A8DE076
A<< (0000+2) (24ms) 6D00
```
This is `6D00` is not the reply what we were hoping for. Closer inspection shows that our message is prepended
with `34303000 0C` which is probably the reason we are not getting the intended reply.

# Debugging
To be done using [this](https://www.javacardos.com/tools/pyresman) tool