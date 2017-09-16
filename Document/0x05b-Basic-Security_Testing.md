## Setting up a Testing Environment for Android Apps

By now, you should have a basic understanding of how Android apps are structured and deployed. In this chapter, we'll talk about setting up an environment for security testing and describe basic processes you'll be using. The content of this chapter servers as a foundation for the more detailed testing methods discussed in later chapters.

You can set up a fully functioning test environment on almost any machine running Windows, Linux or Mac OS. 

#### Software Needed on the Host PC or Mac

At the very least, you'll need [Android Studio](https://developer.android.com/studio/index.html "Android Studio") (which comes with the Android SDK) platform tools and emulator, and a manager app to manage the various SDK versions and framework components. With Android Studio, you also get an SDK Manager app that lets you install the Android SDK tools and manage SDKs for various API levels, as well as the emulator and an AVD Manager application to create emulator images. Make sure that the newest [SDK tools](https://developer.android.com/studio/index.html#downloads) and [platform tools](https://developer.android.com/studio/releases/platform-tools.html) packages are installed on your system.

#### Setting up the Android SDK

Local Android SDK installations are managed through Android Studio. Create an empty project in Android Studio and select "Tools->Android->SDK Manager" to open the SDK Manager GUI. The "SDK Platforms" tab lets you install SDKs for multiple API levels. Recent API levels are:

- API 23: Android 6.0
- API 24: Android 7.0
- API 25: Android 7.1
- API 26: Android 8.0

<img src="Images/Chapters/0x05c/sdk_manager.jpg" width="500px"/>

Installed SDKs are found at the following locations:

```
Windows:

C:\Users\<username>\AppData\Local\Android\sdk

MacOS:

/Users/<username>/Library/Android/sdk
```

Note: On Linux, you'll need to pick your own SDK location. `/opt`, `/srv`, and `/usr/local` are common locations.

#### Testing on a Real Device

If you are planning to do dynamic analysis, you'll need an Android device to run the target app on. In principle, you can do without a real Android device and test purely on the emulator. However, apps execute quite slowly on the emulator, which can make security testing tedious. Testing on a real device makes for a smoother process and a more realistic environment.

When testing on a real device, it is recommended to *root* the device (i.e., modifying the OS so that you can run commands as the root user). This gives you full control over the operating system and allows you to bypass restrictions such as app sandboxing. This in turn allows you to perform techniques like code injection and function hooking more easily.

Note however that rooting is risky, and three main consequences need to be clarified before you may proceed. Rooting can have the following negative effects:

- Usually voids the device warranty (always check the manufacturer policy before taking any action),
- May "brick" the device, i.e., render it inoperable and unusable.
- Brings additional security risks as built-in exploit mitigations are often removed.

You should not root a personal device that you also use to store your private information. Instead, we recommend getting a cheap, dedicated test device. Many older devices, such as Google's Nexus series, can run the newest Android versions and are perfectly fine for testing.

**You need to understand that rooting your device is ultimately YOUR own decision and that OWASP shall in no way be held responsible for any damage. In case you feel unsure, always seek expert advice before starting the rooting process.**

###### What Mobiles Can Be Rooted?

Virtually any Android mobile can be rooted. Commercial versions of Android OS, at the kernel level evolutions of Linux OS, are optimized for the mobile world. Here some features are removed or disabled, such as the possibility for a non-privileged user to become the 'root' user (who has elevated privileges). Rooting a phone means adding the feature to become the root user, e.g. technically speaking adding a standard Linux executable called `su` used for switching users.

The first step in rooting a mobile is to unlock its boot loader. The procedure depends on each manufacturer. However, for practical reasons, rooting some mobiles is more popular than rooting others, particularly when it comes to security testing: devices created by Google (and manufactured by other companies like Samsung, LG and Motorola) are among the most popular, particularly because they are widely used by developers. The device warranty is not nullified when the boot loader is unlocked and Google provides many tools to support the root itself to work with rooted devices. A curated list of guide on rooting devices from all major brands can be found in the [XDA forums](https://www.xda-developers.com/root/ "Guide to rooting mobile devices").

##### Network Setup

The available setup options for the network need to be evaluated first. The mobile device used for testing and the machine running the interception proxy need to be placed within the same WiFi network. Either an (existing) access point is used or [an ad-hoc wireless network is created](https://support.portswigger.net/customer/portal/articles/1841150-Mobile%20Set-up_Ad-hoc%20network_OSX.html "Creating an Ad-hoc Wireless Network in OS X").

Once the network is configured and connectivity is established between the testing machine and the mobile device, several other steps need to be done.

- The proxy in the network settings of the Android device need to be [configured properly to point to the interception proxy in use](https://support.portswigger.net/customer/portal/articles/1841101-Mobile%20Set-up_Android%20Device.html "Configuring an Android Device to Work With Burp").
- The [CA certificate of the interception proxy need to be added to the trusted certificates in the certificate storage of the Android device](https://support.portswigger.net/customer/portal/articles/1841102-installing-burp-s-ca-certificate-in-an-android-device "Installing Burp's CA Certificate in an Android Device"). Due to different versions of Android and modifications of Android OEMs to the settings menu, the location of the menu to store the CA certificate might differ.

After finishing these steps and starting the app, the requests should show up in the interception proxy.

#### Testing on the Emulator

All of the above steps to prepare a hardware testing device do also apply if an emulator is used. For dynamic testing several tools or VMs are available that can be used to test an app within an emulator environment:

- AppUse
- MobSF
- Nathan

You can also easily create Android Virtual Devices (AVDs) via Android Studio.

##### Setting Up a Web Proxy on a Virtual Device

To set up a HTTP proxy on the emulator follow the following procedure, which works on the Android emulator shipping with Android Studio 2.x:

1. Set up your proxy to listen on localhost. Reverse-forward the proxy port from the emulator to the host, e.g.:

```bash
$ adb reverse tcp:8080 tcp:8080
```

2. Configure the HTTP proxy in the access point settings of the device:
- Open the Settings Menu
- Tap on "Wireless & Networks" -> "Cellular Networks" or "Mobile Networks"
- Open "Access Point Names"
- Open the existing APN (e.g. "T-Mobile US")
- Enter "127.0.0.1" in the "Proxy" field and your proxy port in the "Port" field (e.g. "8080")
- Open the top-right menu and tap "save"

<img width=300px src="Images/Chapters/0x05b/emulator-proxy.jpg"/>

HTTP and HTTPS requests should now be routed over the proxy on the host machine. Try toggling airplane mode off and on if it doesn't work.

##### Installing a CA Certificate on the Virtual Device

An easy way to install a CA certificate is pushing the cert to the device and adding it to the certificate store via Security Settings. For example, you can install the PortSwigger (Burp) CA certificate as follows:

1. Start Burp and navigate to http://burp/ using a web browser on the host, and download `cacert.der` by clicking the "CA Certificate" button.
2. Change the file extension from `.der` to `.cer`
3. Push the file to the emulator:

```bash
$ adb push cacert.cer /sdcard/
```

4. Navigate to "Settings" -> "Security" -> "Install from SD Card"
5. Scroll down and tap on `cacert.cer`

You should now be prompted to confirm installation of the certificate (you'll also be asked to set a device PIN if you haven't already).

##### Connecting to an Android Virtual Device (AVD) as Root

An Android Virtual Device (AVD) can be created by using the AVD manager, which is [available within Android Studio](https://developer.android.com/studio/run/managing-avds.html "Create and Manage Virtual Devices"). The AVD manager can also be started separately from the command line by using the `android` command in the tools directory of the Android SDK:

```bash
$ ./android avd
```

Once the emulator is up and running a root connection can be established by using `adb`.

```bash
$ adb root
$ adb shell
root@generic_x86:/ $ id
uid=0(root) gid=0(root) groups=0(root),1004(input),1007(log),1011(adb),1015(sdcard_rw),1028(sdcard_r),3001(net_bt_admin),3002(net_bt),3003(inet),3006(net_bw_stats) context=u:r:su:s0
```

Rooting of an emulator is therefore not needed as root access can be granted through `adb`.

##### Restrictions When Testing on an Emulator

There are several downsides when using an emulator. You might not be able to test an app properly in an emulator, if it's relying on the usage of a specific mobile network, or uses NFC or Bluetooth. Testing within an emulator is usually also slower in nature and might lead to issues on its own.

Nevertheless several hardware characteristics can be emulated, including [GPS](https://developer.android.com/studio/run/emulator-commandline.html#geo "GPS Emulation"), [SMS](https://developer.android.com/studio/run/emulator-commandline.html#sms "SMS") and many more.

### Testing Methods

#### Manual Static Analysis

In principle, we talk about white-box testing when the source code (or even better, the complete Android Studio project) is available, and black-box if only APK package is available. In Android app security testing however, the difference is not all that big. The majority of apps can be decompiled easily, and with some reverse engineering knowledge, having access to bytecode and binary code is almost as good as having the original code, except in cases where the release build is purposefully obfuscated.

To accomplish the source code testing, you will want to have a setup similar to the developer. You will need a testing environment on your machine with the Android SDK and an IDE installed. It is also recommended to have access either to a physical device or an emulator, so you can debug the app.

During **Black box testing** you will not have access to the source code in its original form. Usually, you will have the application package in hand (in [Android .apk format](https://en.wikipedia.org/wiki/Android_application_package "Android application package"), which can be installed on an Android device or reverse engineered with the goal to retrieve parts of the source code.

In case you need to pull the APK from the device, the following steps should be followed:

```bash
$ adb shell pm list packages
(...)
package:com.awesomeproject
(...)
$ adb shell pm path com.awesomeproject
package:/data/app/com.awesomeproject-1/base.apk
$ adb pull /data/app/com.awesomeproject-1/base.apk
```

An easy way on the CLI to retrieve the source code of an APK is through `apkx`, which also packages `dex2jar` and CFR and automates the extracting, conversion and decompilation steps. Install it as follows:

```
$ git clone https://github.com/b-mueller/apkx
$ cd apkx
$ sudo ./install.sh
```

This should copy `apkx` to `/usr/local/bin`. Run it on the APK that need to be tested:

```bash
$ apkx UnCrackable-Level1.apk
Extracting UnCrackable-Level1.apk to UnCrackable-Level1
Converting: classes.dex -> classes.jar (dex2jar)
dex2jar UnCrackable-Level1/classes.dex -> UnCrackable-Level1/classes.jar
Decompiling to UnCrackable-Level1/src (cfr)
```

If the application is based solely on Java and does not have any native library (code written in C/C++), the reverse engineering process is relatively easy and recovers almost the entire source code. Nevertheless, if the code is obfuscated, this process might become very time consuming and might not be productive. The same applies for applications that contain a native library. They can still be reverse engineered but require low level knowledge and the process is not automated.

More details and tools about the Android reverse engineering topic can be found in the "Tampering and Reverse Engineering on Android" section.

#### Automated Static Analysis

Static analysis should be supported through the usage of tools, to make the analysis efficient and to allow the tester to focus on the more complicated business logic. There are a plethora of static code analyzers that can be used, ranging from open source scanners to full blown enterprise ready scanners. The decision on which tool to use depends on your budget, requirements by the client and the preferences of the tester.

Some static analyzers rely on the availability of the source code while others take the compiled APK as input.
It is important to keep in mind that while static analyzers can help us to focus attention on potential problems, they may not be able to find all the problems by itself. Go through each finding carefully and try to understand what the app is doing to improve your chances of finding vulnerabilities.

One important thing to note is to configure the static analyzer properly in order to reduce the likelihood of false positives and maybe only select several vulnerability categories in the scan. The results generated by static analyzers can otherwise be overwhelming and the effort can become counterproductive if an overly large report need to be manually investigated.

Automated open source tools for performing security analysis on an APK are:

- [QARK](https://github.com/linkedin/qark/ "QARK")
- [Androbugs](https://github.com/AndroBugs/AndroBugs_Framework "Androbugs")
- [JAADAS](https://github.com/flankerhqd/JAADAS "JAADAS")

See also the section "Static Source Code Analysis" for enterprise tools in the chapter "Testing Tools".

#### Dynamic Analysis

Compared to static analysis, dynamic analysis is applied while executing the mobile app. The test cases can range from investigating the file system and changes made to it on the mobile device to monitoring the communication with the endpoint while using the app.

When we talk about dynamic analysis of applications that rely on the HTTP(S) protocol, several tools can be used to support the dynamic analysis. The most important tools are so called interception proxies, like OWASP ZAP or Burp Suite Professional to name the most famous ones. An interception proxy allows the tester to have a Man-in-the-middle position in order to read and/or modify all requests made from the app and responses coming from the endpoint for testing Authorization, Session Management and so on.

##### Drozer

[Drozer](https://github.com/mwrlabs/drozer "Drozer on GitHub") is an Android security assessment framework that allows you to search for security vulnerabilities in apps and devices by assuming the role of a third party app interacting with the other application's IPC endpoints and the underlying OS. The following section documents the steps necessary to install and begin using Drozer.

###### Installing Drozer

**On Linux:**

Pre-built packages for many Linux distributions are available on the [Drozer website](https://labs.mwrinfosecurity.com/tools/drozer/ "Drozer Website"). If your distribution is not listed, you can build Drozer from source as follows:

```
git clone https://github.com/mwrlabs/drozer/
cd drozer
make apks
source ENVIRONMENT
python setup.py build
sudo env "PYTHONPATH=$PYTHONPATH:$(pwd)/src" python setup.py install
```

**On Mac:**

On Mac, Drozer is a bit more difficult to install due to missing dependencies. Specifically, Mac OS versions from El Capitan don't have OpenSSL installed, so compiling pyOpenSSL doesn't work. You can resolve those issues by [installing OpenSSL manually]. To install openSSL, run:

```
$ brew install openssl
```

Drozer also depends on older versions of some libraries. In order not to mess up the system Python setup, it is better to install Python with homebrew and creating a dedicated environment with virtualenv (using a Python version management tool like [pyenv](https://github.com/pyenv/pyenv "pyenv") is even better, but setting this up is beyond the scope of this book).

Install virtualenv via pip:

```
$ pip install virtualenv
```

Create a project directory to work in - you'll download several files into that directory. Change into the newly created directory and run the command `virtualenv drozer`. This creates a "drozer" folder which contains the Python executable files and a copy of the pip library.

```
$ virtualenv drozer
$ source drozer/bin/activate
(drozer) $
```

You're now ready to install the required version of pyOpenSSL and build it against the OpenSSL headers installed previously. The pyOpenSSL version required by Drozer has a typo that prevents it from compiling successfully, so need to fix the source before compiling. Fortunately, ropnop has figured out necessary steps and documented them in a [blog post](https://blog.ropnop.com/installing-drozer-on-os-x-el-capitan/ "ropnop Blog - Installing Drozer on OS X El Capitan").
Run the following commands:

```
$ wget https://pypi.python.org/packages/source/p/pyOpenSSL/pyOpenSSL-0.13.tar.gz
$ tar xzvf pyOpenSSL-0.13.tar.gz
$ cd pyOpenSSL-0.13
$ sed -i '' 's/X509_REVOKED_dup/X509_REVOKED_dupe/' OpenSSL/crypto/crl.c
$ python setup.py build_ext -L/usr/local/opt/openssl/lib -I/usr/local/opt/openssl/include
$ python setup.py build
$ python setup.py install
```

With that out of the way, you can install the remaining dependencies.

```
$ easy_install protobuf==2.4.1 twisted==10.2.0
```

Finally, download and install the Python .egg from the MWR labs website:

```
$ wget https://github.com/mwrlabs/drozer/releases/download/2.3.4/drozer-2.3.4.tar.gz
$ tar xzf drozer-2.3.4.tar.gz
$ easy_install drozer-2.3.4-py2.7.egg
```

**Installing the Agent:**

Drozer agent is the component running on the device itself. Download the latest Drozer Agent [here](https://github.com/mwrlabs/drozer/releases/), and install it with adb.

```
$ adb install drozer.apk
```

**Starting a Session:**

You should now have the Drozer console installed on your host machine, and the Agent running on your USB-connected device or emulator. Now, you need to connect the two and you’re ready to start exploring.

Open the Drozer application in running emulator and click the OFF button in the bottom of the app which will start a Embedded Server.

![alt text](Images/Chapters/0x05b/server.png "Drozer")

By default the server listens on port 31415. Forward this port to the localhost interface using adb, then run Drozer on the host to connect to the agent.

```bash
$ adb forward tcp:31415 tcp:31415
$ drozer console connect
```

To show the list of all Drozer modules that can be executed in the current session use the "list" command.

**Basic Drozer Commands:**

- To list out all the packages installed on the emulator, run the following command:

	`dz> run app.package.list`

 * To find out the package name of a specific app, pass  the “-f” along with a search string:

	`dz> run app.package.list –f (string to be searched)`

- To see some basic information about the package, use

  	`dz> run app.package.info –a (package name)`

- To identify the exported application components, run the following command:

  	`dz> run app.package.attacksurface (package name)`

- To identify the the list of Activities exported in the target application, execute the following command:

  	`dz> run app.activity.info -a (package name)`

 * To launch the activities exported,run the following command:

   	`dz> run app.activity.start --component (package name) (component name)`

**Using Modules:**

Out of the box, Drozer provides modules to investigate various aspects of the Android platform, and a few remote exploits. You can extend Drozer's functionality by downloading and installing additional modules.

**Finding Modules:**

The official Drozer module repository is hosted alongside the main project on Github. This is automatically setup in your copy of Drozer. You can search for modules using the `module` command:

```bash
dz> module search tool
kernelerror.tools.misc.installcert
metall0id.tools.setup.nmap
mwrlabs.tools.setup.sqlite3
```

For more information about a module, pass the `–d` option to view the module's description:

```
dz> module  search url -d
mwrlabs.urls
    Finds URLs with the HTTP or HTTPS schemes by searching the strings
    inside APK files.

        You can, for instance, use this for finding API servers, C&C
    servers within malicious APKs and checking for presence of advertising
    networks.

```

**Installing Modules:**

You can install modules using the `module` command:

```
dz> module install mwrlabs.tools.setup.sqlite3
Processing mwrlabs.tools.setup.sqlite3... Already Installed.
Successfully installed 1 modules, 0 already installed
```

This will install any module that matches your query. Newly installed modules are dynamically loaded into the
console and are available for immediate use.

#### Network Monitoring/Sniffing

On Android it is possible to [remotely sniff all traffic in real-time by using tcpdump, netcat (nc) and Wireshark](http://blog.dornea.nu/2015/02/20/android-remote-sniffing-using-tcpdump-nc-and-wireshark/ "Android remote sniffing using Tcpdump, nc and Wireshark"). First ensure you have the latest version of [Android tcpdump](http://www.androidtcpdump.com/) on your phone. Here are the [installation steps](https://wladimir-tm4pda.github.io/porting/tcpdump.html "Installing tcpdump"):

```
# adb root
# adb remount
# adb push /wherever/you/put/tcpdump /system/xbin/tcpdump
```

When executing `adb root` you might get an error saying `adbd cannot run as root in production builds`. If that's the case install tcpdump like this:

```
# adb push /wherever/you/put/tcpdump /data/local/tmp/tcpdump
# adb shell
# su
$ mount -o rw,remount /system;
$ cp /data/local/tmp/tcpdump /system/xbin/
```

> Remember: In order to use `tcpdump` you need root privileges on the phone!

`tcpdump` should now be working, so execute it once to see if it does. Once a few packets are coming in you can stop it by pressing CTRL+c.

```
# tcpdump
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on wlan0, link-type EN10MB (Ethernet), capture size 262144 bytes
04:54:06.590751 00:9e:1e:10:7f:69 (oui Unknown) > Broadcast, RRCP-0x23 reply
04:54:09.659658 00:9e:1e:10:7f:69 (oui Unknown) > Broadcast, RRCP-0x23 reply
04:54:10.579795 00:9e:1e:10:7f:69 (oui Unknown) > Broadcast, RRCP-0x23 reply
^C
3 packets captured
3 packets received by filter
0 packets dropped by kernel
```

The first step in order to do remote sniffing of the network traffic on the Android phone is by executing `tcpdump` and pipe its output to netcat (nc):

```
$ tcpdump -i wlan0 -s0 -w - | nc -l -p 11111
```

The tcpdump command above is
- listening on the interface wlan0,
- defines the size (snaplength) of the capture in bytes to get everything (-s0) and is
- writing to a file (-w), but instead of a filename we provide `-` which will make tcpdump to write to stdout.

With the pipe (`|`) we sent all output from tcpdump to netcat that opens a listener on port 11111. Usually you want to monitor the wlan0 interface, in case you need another interface just list the available ones with `$ ip addr`.

In order to access port 11111 on the Android phone opened by netcat, we need to forward the port via adb to your machine.

```
$ adb forward tcp:11111 tcp:11111
```

With the following command you are connecting to the forwarded port available on your local machine via netcat and piping it to Wireshark.

```
$ nc localhost 11111 | wireshark -k -S -i -
```

Wireshark should start now immediately (-k) and get's all data from stdin (-i -) via netcat that is connecting to the forwarded port. You should see now all the traffic from the wlan0 interface from the Android phone.

<img src="Images/Chapters/0x05b/Android_Wireshark.png" width="350px"/>


#### Firebase/Google Cloud Messaging (FCM/GCM)

Firebase Cloud Messaging (FCM) is the successor of Google Cloud Messaging (GCM) and is a free service offered by Google and allows to send messages between an application server and client apps. The server and client app are communicating via the FCM/GCM connection server that is handling the downstream and upstream messages.

![Architectural Overview](Images/Chapters/0x05b/FCM-notifications-overview.png)

Downstream messages are sent from the application server to the client app (push notifications); upstream messages are sent from the client app to the server.

FCM is available for Android and also for iOS and Chrome. FCM provides two connection server protocols at the moment: HTTP and XMPP and there are several differences in the implementation, as described in the [official documentation](https://firebase.google.com/docs/cloud-messaging/server#choose "Differences of HTTP and XMPP in FCM"). The following example demonstrates how to intercept both protocols.

##### Preparation

FCM can use two different protocols to communicate with the Google backend, either XMPP or HTTP.

**HTTP**

The ports used by FCM for HTTP are 5228, 5229, and 5230. Typically only 5228 is used, but sometimes also 5229 or 5230 is used.

- Configure a local port forwarding on your machine for the ports used by FCM. The following example can be used on Mac OS X:

```bash
$ echo "
rdr pass inet proto tcp from any to any port 5228-> 127.0.0.1 port 8080
rdr pass inet proto tcp from any to any port 5229 -> 127.0.0.1 port 8080
rdr pass inet proto tcp from any to any port 5239 -> 127.0.0.1 port 8080
" | sudo pfctl -ef -
```

- The interception proxy need to listen to the port specified in the port forwarding rule above, which is 8080.

**XMPP**

The [ports used by FCM over XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref "Firebase via XMPP") are 5235 (Production) and 5236 (Testing).

- Configure a local port forwarding on your machine for the ports used by FCM. The following example can be used on Mac OS X:

```bash
$ echo "
rdr pass inet proto tcp from any to any port 5235-> 127.0.0.1 port 8080
rdr pass inet proto tcp from any to any port 5236 -> 127.0.0.1 port 8080
" | sudo pfctl -ef -
```

- The interception proxy need to listen to the port specified in the port forwarding rule above, which is 8080.

##### Intercepting Messages

Look also into the chapter "Testing Network Communication" and the test case "Man-in-the-middle (MITM) attacks" for further preparation steps and to get ettercap running.

Your testing machine and the Android device need to be in the same wireless network. Start ettercap with the following command and replace the IP addresses with the one of the Android device and the network gateway in the wireless network.

```bash
$ sudo ettercap -T -i en0 -M arp:remote /192.168.0.1// /192.168.0.105//
```

Start using the app and trigger a function that uses FCM. You should see HTTP messages showing up in your interception proxy.

![Intercepted Messages](Images/Chapters/0x05b/FCM_Intercept.png)

> When using ettercap you need to activate "Support invisible proxying" in Proxy Tab / Options / Edit Interface

Interception proxies like Burp or OWASP ZAP will not show this traffic, as they are not capable of decoding it properly by default. There are however Burp plugins such as [Burp-non-HTTP-Extension](https://github.com/summitt/Burp-Non-HTTP-Extension) and [Mitm-relay](https://github.com/jrmdev/mitm_relay) that visualize XMPP traffic.


#### Potential Obstacles

For the following security controls that might be implemented into the app you are about to test, it should be discussed with the project team if it is possible to provide a debug build. A debug build has several benefits when provided during a (white box) test, as it allows a more comprehensive analysis.

##### Certificate Pinning

If the app implements certificate pinning, C.509 certificates provided by an interception proxy are declined and the app will refuse to make any requests through the proxy. To be able to efficiently test during a white box test, a debug build with deactivated certificate pinning should be provided.

For a black box test, there are several ways to bypass certificate pinning, for example [SSLUnpinning](https://github.com/ac-pm/SSLUnpinning_Xposed "SSLUnpinning") or [Android-SSL-TrustKiller](https://github.com/iSECPartners/Android-SSL-TrustKiller "Android-SSL-TrustKiller"). Therefore bypassing can be done within seconds, but only if the app uses the API functions that are covered for these tools. If the app is using a different framework or library to implement SSL Pinning that is not implemented yet in those tools, the patching and deactivation of SSL Pinning needs to be done manually and can become time consuming.

To manually deactivate SSL Pinning there are two ways:
- Dynamical Patching while running the App, by using [Frida](https://www.frida.re/docs/android/ "Frida") or [ADBI](https://github.com/crmulliner/adbi "ADBI")
- [Identify the SSL Pinning logic in smali code, patch it and reassemble the APK](https://serializethoughts.com/2016/08/18/bypassing-ssl-pinning-in-android-applications/ "Bypassing SSL Pinning in Android Applications")

Once successful, the prerequisites for a dynamic analysis are met and the apps communication can be investigated.

See also test case "Testing Custom Certificate Stores and Certificate Pinning" for further details.

##### Root Detection

Root detection can be implemented using pre-made libraries like [RootBeer](https://github.com/scottyab/rootbeer "RootBeer") or custom checks. An extensive list of root detection methods is presented in the "Testing Anti-Reversing Defenses on Android" chapter.

In a typical mobile app security build, you'll usually want to test a debug build with root detection disabled. If such a build is not available for testing, root detection can be disabled using a variety of methods which will be introduced later in this book.
