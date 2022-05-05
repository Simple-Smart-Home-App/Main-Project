*CS455 - Final Project*
---

*Authors*
---
John Stevenson

Sam Deshazer

Minh Nguyen

Patrick Tsai

Levi Benning

*Description*
---
This is the Simple Smart Home App main project repository. It contains 
submodule directories for the three major subsystems which make up the 
application. 

The Android front end application provides a mobile
user interface for interacting with IoT connected appliances, in this case an 
LED. It is located in two separate directories, one labeled as Front-End-App 
which contains the Kotlin implementation of the applicaion, the other, labeled as 
FrontEndAppJava contains the Java version of the front end applicaion.

The IoT Appliance Controller application is an Arduino sketch written for an
ESP8266 NodeMCU device which is directly connected to the home appliances. The
NodeMCU periodically (every 250ms) polls the AWS backend server to retrieve
desired state information for attachced appliances, in this case an onboard
LED. The application source code is located in the IoT_Appliance_Controller_App
directory.

The backend application is an AWS hosted MySQL server which stores
the desired state information for the home appliances. This state information is 
updated by the front end Android application, and can be read by both the front end
app as well as the IoT appliance controller device. Both the Android app and the 
IoT controller communicate with the backend server via HTTPS. The back end 
application code is contained within the Backend_API_SmartHomeApp directory.

Further information about code authors, compilation, and running the submodule 
code is provided below.

*Android Application*
---

*Authors*
---
John Stevenson

Sam Deshazer

Minh Nguyen

*Description*
---
This android application is a public interface which controls 
an LED through a REST api backend. One can turn the LED on and 
off through the app by pressing a button. The screen will 
indicate whether the LED is on or off, so the user will know the 
status of the LED even if they cannot see it.

For the Java version of the application, locate the build.gradle(app) 
under Gradle Scripts in the project heirarchy on the left. Here is where 
the android SDK versions are set, be sure that these match with your gradle settings
found in File > Settings > Build, Execution, Deployment > Build Tools > Gradle.
and the SDK needed can be downloaded through Android Studio at 
Tools > SDK Manager > Android SDK > SDK Tools (you may check Show Pakage Details 
on the bottom left to see various version to install and select Apply when finished)

Required to compile with Android Studio:

Java 11 Gradle Version

compileSdk 31 (Android SDK 31)

minSdk 21

targetSdk 31

buildToolsVersion '30.0.3'

Dependencies found in build.gradle(app):

    implementation 'androidx.appcompat:appcompat:1.4.1'
    implementation 'com.google.android.material:material:1.5.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.3'
    implementation 'com.android.volley:volley:1.2.1'
    implementation 'org.jetbrains:annotations:15.0'
    testImplementation 'junit:junit:4+'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
    


Make sure to sync the gradle accordingly after finishing all of the above 
before building File > Sync Gradle With Proeject Files

Build and run with a designated AVD (can be set in AVD manager - specified by Android Studio)


*Java API - JAX-RS implementation*
----------------------------------

*Authors*
---------
Patrick Tsai

*Description*
---
We are using a MySQL database to hold the state value for our light (1 for "on", and 0 for "off").

To access and change the light status in our MySQL database we are using a Java API we created ourselves.
Our API consists of 2 endpoints:
    - GET https://test.wsuv-cs-project.com:8443/cs455Backend-1.0-SNAPSHOT/api/state/rds/get?id=1
    - PUT https://test.wsuv-cs-project.com:8443/cs455Backend-1.0-SNAPSHOT/api/state/rds/update?id=1&status=0
    
    API contollers directory: Backend_API_SmartHomeApp/src/main/java/dev/patricktsai/cs455backend/controller/
    Data Access Object directory: Backend_API_SmartHomeApp/src/main/java/dev/patricktsai/cs455backend/dao/
    CORS Filter location: Backend_API_SmartHomeApp/src/main/java/dev/patricktsai/cs455backend/

The Java API (JAX-RS) resides on an AWS EC2 t2.micro cloud server

Server info:
    - AWS EC2 t2.micro
    - Linux 2 OS
    - runs Apache Tomcat webserver
    - SSL/TLS Let's Encrypt/certbot certificate authority
    - Java 11
    - codebase Java Enterprise Edition (EE)

DataBase info:
    - AWS RDS instance
    - running MySQL server

Video testing API with Postman (1 min): https://www.youtube.com/watch?v=tXmDfohJmzk


*IoT Appliance Controller Application*
---

*Authors*
---
Levi Benning

*Description*
---
This is a simple Arduino sketch, written for the ESP8266 NodeMCU. The sketch 
attempts to connect to a cloud-based AWS SQL server via an HTTPS connection 
to read a binary state value using a REST API. Based on the 
returned value of the state variable, the NodeMCU updates the state of its
onboard LED to be either on or off.

The program utilizes Wifi credentials provided by the user in the programs header 
file, including the SSID and password of the network represented as string literals. 
The program also stores the SHA-1 fingerprint (for the HTTPS connection) of the 
AWS server as an array of 20, 1 byte hex characters. In the event that the program is 
unable to establish an HTTPS connection to the server, the fingerprint may need to be updated.
The user can use a web browser such as Google Chrome, to connect to the servers URL
to determine the new fingerprint, which they would then use to update the fingerprint
array in the program.

To run the program, the user first needs to configure their Arduino IDE to communicate
with the ESP8266 NodeMCU using the instructions provided below:

https://randomnerdtutorials.com/how-to-install-esp8266-board-arduino-ide/

The user would then update the projects header file to match the credentials of the
Wifi network they would like to connect to. PLEASE NOTE that the NodeMCU only
uses the 2.4 Ghz Wifi band, not 5.8 Ghz, so make sure your credentials match
that of the 2.4 Ghz network prior to compilation! The user would then complile 
and load the sketch onto the board in the usual manner. If the user wants to test 
the LED changing functionality of the program without also running the companion 
Android application, they can use an application such as Postman to send REST 
commands to the AWS server to update the state variable. 
