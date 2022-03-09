Mobile Automation with Detox 

Gray box end-to-end testing and automation library for mobile apps.

As per the Detox official Documentation :

High velocity native mobile development requires us to adopt continuous integration workflows, which means our reliance on manual QA has to drop significantly. Detox tests your mobile app while it’s running in a real device/simulator, interacting with it just like a real user.
The most difficult part of automated testing on mobile is the tip of the testing pyramid - E2E. The core problem with E2E tests is flakiness - tests are usually not deterministic. We believe the only way to tackle flakiness head on is by moving from black box testing to gray box testing. That’s where Detox comes into play.
Cross Platform: Write cross-platform tests in JavaScript. Currently supports iOS and Android.
Runs on Devices (not yet supported on iOS): Gain confidence to ship by testing your app on a device/simulator just like a real user.
Automatically Synchronized: Stops flakiness at the core by monitoring asynchronous operations in your app.
Made For CI: Execute your E2E tests on CI platforms like Travis without grief.
Test Runner Independent: Use Jest, Mocha, AVA, or any other JavaScript test runner you like (spoiler: we have our favorite).
Debuggable: Modern async-await API allows breakpoints in asynchronous tests to work as expected.
Remember the term  async-await , that’s how detox are working to remove the flakiness (if you are interested in knowing more there is a small section of it below , rest can be googled :) )

 

How’s Detox different from other Mobile testing apps :

Detox does not rely on WebDriver — Detox is built from the ground up to integrate with native layers of your mobile app directly, Meaning it avoids unnecessary wrapping of instructions via Drivers through JSON wire protocol instead interacts directly with Native layer like presentation layer , Business etc.
Detox Does gray box testing instead of black box because switching to gray box allows the test framework to monitor the app from the inside and delivers critical wins like fighting flakiness at the core.
Built from the ground up for native mobile and has a first-class support for React Native apps

Important read : How Detox Automatically Synchronizes With Your App​
One of the key features of Detox is its ability to automatically synchronize the test execution with your app. The most annoying aspect of end-to-end tests is flakiness—tests sometimes fail without anything changing. Flakiness happens because tests are nondeterministic. Every time a test is running, things take place in a slightly different order inside your app.
Consider a scenario where the app is making multiple network requests at the same time. What is the order of execution? It depends on which request completes first. This is an external concern depending on network congestion and how busy the server is.
The traditional method of dealing with flakiness is adding various sleep()/waitFor() commands throughout the test in an attempt to force a certain execution order. This is a bad practice, riddled with fragile magic values that often change if the machine running the tests becomes faster or slower.
Detox tries to eliminate flakiness by automatically synchronizing your tests with the app. A test cannot continue to the next command until the app becomes idle. Detox monitors your app very closely in order to know when it’s idle. It tracks several asynchronous operations and waits until they complete. This includes:
Keeping track of all network requests that are currently in-flight and waiting until they complete
Keeping track of pending animations and waiting until they complete
Keeping track of timers and waiting until they expire or are canceled
Keeping track of the React Native operations

Want to Understand more on internal working of detox read here: Detox sync

Things to have (Prerequisites)  : 
Install Node JS
Install Detox CLI 
Install Platform Specific Dependencies like Android/IOS
Create a React JS project (Use specific CLI like NPM or epox etc.)
Setup Gradle 
Setup Detox Node Module and apply configuration
Setup Test Runner like Jest or Mocha
Build and Run Detox App

Installing Node JS : Mac and Windows	
Mac →  a) Download homebrew  Install Homebrew
     	   b) Run → brew install node and setup path as system variable 
Windows : 
→ download Node JS installer from official website Install NodeJs  
→  Execute exe once done .
→ Setup the Node JS Path as system variables and add Bin path to path variables (refer below screenshot) 
System properties → Environment variables -> System variables > path (add new)



2) Install Detox Command Line Tools 
Works for both mac and windows → npm install -g detox-cli
This Allows you to download node module package built for detox 
3) Install Platform Specific Dependencies like Android/IOS
→ Install java and verify version , also set java path in system variables .
For Reference : 
On MacOS, in particular, Java comes from both the OS and possibly other installers such as homebrew. That can really get things tangled up. 
To mitigate:
Use one of the options suggested in this Stack Overflow post.
Install OpenJDK 11 on top of the existing versions (how to check?): https://techoral.com/blog/java/install-openjdk-11-on-mac.html. Consider employing the JAVA_HOME variable to get things to work right. Note: This is more suitable if your environment is fairly clean, and does not contain versions from 3rd-party installers (e.g. homebrew).
Use these refs, which might be useful:
https://java.com/en/download/faq/java_mac.xml#version
https://www.java.com/en/download/help/version_manual.xml

 Installing Android SDK 
Follow the instructions under this blog and everything is well documented  → Install Android for windows and mac


Or You can follow the Command line tools for setting up path variables as mentioned here
https://developer.android.com/studio/command-line/variables
Download and install Android Emulators
There are 2 ways to setup android Emulators (read through before deciding) 


1st :
Download Android studio for windows 
Install and configure the studio as with default instructions
Add AVD to run the emulators with google API’s 

Note : Instructions are given below (Don’t rush)

2nd:  Follow AOSP emulators (Android Open-Source Project) by google. Detox strongly recommends this but remember it’s not mandatory .

Difference between them is that AOSP emulators don’t have some of the google features but it helps them to reduce the flakiness in the test , choice is yours (Nothing mandatory here) .

Below image tells you the difference ( on left → emulator with google api’s and on Right → is without them) 






Setting Android Studio 
Download Android studio from here
Follow the setup instructions and select any blank project once done .
Post setup below options will be visible under IDE 




Select Device Manager and add any virtual device of your choice , follow the instructions to add the desired device and other components by clicking ‘Next’ .


 











Download the desired API’s and it’s Image 



Once done Successfully , you can launch the new Emulator by clicking on the Emulator option or start button as shown below. (My Emulator is with Google API)

Here’s how to install Android Emulator without Google API using the command line 


While it’s possible to do this using Android Studio, we’ll focus on the command line, as it is also good for headless CI machines.
Locate your 'Android home' folder - typically set in the ANDROID_HOME environment variable, or in its successor - ANDROID_SDK_ROOT. If ANDROID_HOME isn’t set, either set it yourself or run the following commands after cd-ing into the home folder.
Preliminary: Upgrade your emulator executable to the latest version. Note: It is OK if the emulator’s version is not aligned with the SDK or platform-tools' version you currently have installed (e.g. 30.x.x vs. SDK 29)
$ANDROID_HOME/tools/bin/sdkmanager --install emulator
 
Install an emulator image without Google APIs:
$ANDROID_HOME/tools/bin/sdkmanager "system-images;android-28;default;x86_64"
$ANDROID_HOME/tools/bin/sdkmanager --licenses
With ;android-28;, we assumed SDK 28 here, but other APIs are supported just the same.
The ;default; part replaces ;google_apis;, which is the default, and is what matters here.
If you are running on a M1 you must install a arm64 architecture i.e. system-images;android-28;default;arm64-v8a
Create an emulator (i.e. AVD - Android Virtual Device):
$ANDROID_HOME/tools/bin/avdmanager create avd -n Pixel_API_28_AOSP -d pixel --package "system-images;android-28;default;x86_64"
Pixel_API_28_AOSP is just a suggestion for a name. Any name can work here, even Pixel_API_28 - but you might have to delete an existing non-AOSP emulator, first. In any case, the name used in Detox configuration (typically in package.json) should be identical to this one.
-d pixel will install an emulator with the specs of a Pixel-1 device. Other specs can be used.
--package is the most important argument: be sure to use the same value as you did in part 2, above, with ;default;.
Run avdmanager create --help for the full list of options.
Launch the emulator .
 
Setting up Detox for React JS project 

→ Download Detox CLI if not done (npm install -g detox-cli)
→ Create a new react Js using the Npm command line ( npx react-native init <project_Name>) Remember npx comes with nam 5.2+ and higher.
→  Npx will take care of installing all the required dependencies and components using gradle (don’t worry you don’t need to know gradle per se) 

→ cd <project_name> 
→ run command  :->: npx react-native run-android 

Now are are making some progress 
   



It will run open the simulator if not done already with android studio (see below)


Now Go to the Root of the project where package.json file resides (screenshot) 

This part can be skipped if the project is already created successfully with required dependencies .
If you have a reactJS project already created use npx cli (which we did just above ) now do the following  ‘npm install detox --save-dev’
—---------------Else ::::::::: Do this ---------------------
If you have a project without Node integration (such as a native project), add the following package.json file to the root folder of your project:
{ "name": "<your_project_name>", "version": "0.0.1"}
Name your project in package.json and then run the following command
npm install detox --save-dev --no-package-lock
--------------------------------------------------------------------------------------Once Done successfully You should now have Detox available under node_modules/detox folder (refer screenshot below) 

The Detox Module itself has all it’s desired dependencies , libraries and settings to run the project 
Setting up Detox e2e folder with test runner Jest :::::{}::::::

If jest is not already installed in your project, install it by  npm install jest --save-dev
To create the first Detox config file with Jest , run Detox CLI: detox init -r jest
Once done Successfully it will be the following .

----------------------------------------------------------------------------------------------------------------------
It will create an e2e folder with , config.json ,init.js , and a .spec.js file.
detox init runs these steps, which can be reproduced manually:
Creates an e2e/ folder in your project root
Inside the e2e folder, creates config.json (for jest). 
Inside the e2e folder, create the init.js file. 
Inside e2e folder creates firstTest.e2e.js, replace e2e with spec in the filename
Inside the root folder , create .detoxrc.json , we will be using the package. Json, so delete this file
—----------------------------------------------------------------------------




Adding Detox Configuration : Again it can be achieved 2 ways
	
Add the configuration directly into file .detoxrc.json or add a section as 

Or Add the Detox configuration section directly into the ‘’Package.json” file which is under root folder as shown below.







Once the Configuration is added → Remember APK binary path  can be checked after generating build by using android build tools  → npm install android-build-tools -g 
Path to apk :   ‘/android/app/build/outputs/apk’
Build the project using command : detox build --configuration <your configuration name> in my case it’s marked as android 

Once done run the tests as → detox test --configuration <your configuration name>
I am guessing your next questions will be how is configuration getting picked up to run the particular test’s , well your answer is below where runner config points to env conf which has a regex that matches your given criteria like e2e etc.


Your will be able to run the test cases successfully , considering emulator is able to launch the apk successfully 


