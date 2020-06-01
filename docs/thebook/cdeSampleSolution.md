# C-DEngine Solution Tutorial
This tutorial will walk you step by step through the development process of a C-DEngine bases solution.

## Computer Health Monitoring - A sample Solution for the C-DEngine

This sample will show you how to create a small starter solution using several plugins. It will monitor some essential KPIs of your PC and displays the results in the NMI User Interface. You can follow the tutorial step by step or start at any point using the source code on GitHub.

We go through the following steps:

1) Getting Visual Studio Ready
2) Creating a C-DEngine Host Application
3) Adding the NMI Plugin to the Host
4) Adding plugins from our Plugins Depot
5) Connecting the Host to the Cloud Relay
6) Creating an installer for the Solution
7) Publishing the installer to the WinGet Depot
8) Customizing the NMI Skin
9) Securing the Host using SSL

## Getting Visual Studio Ready

We recommend using the latest version of Visual Studio. By the time of writing this article we were using Visual Studio 2019 16.0.
Make sure you have the following options installed when you install VS:
* ASP.NET and Web Development (.NET Core 3.1)
* .NET Desktop Development
* Universal Windows Platform Development (if you want to include the C-DEngine in a UWP application)
* .NET Core cross-platform development

We also recommend that you install the Visual Studio Project Templates for the C-DEngine. You can find the VSIX here: https://github.com/TRUMPF-IoT/cdeSDK/releases/download/5.105.0/CDEngineSDKTemplates.vsix

## Creating a host
The C-DEngine is a DLL that contains all the necessary services required to create an application but it does require a host to run in. The C-DEngine supports a variety of hosts like IIS, ASP.NET, Docker, Console or Windows Service. This Tutorial will use a .NET Core Console host.

1) To create the host, launch Visual Studio 2019 as Admin and create a new Project using the template "Console Host App for C-Labs C-DEngine (.NET Core)" in your favorite project folder
> We want to use the kernel http.sys as the WebServer for the host. Only admin has access to the http.sys. If you launch VS without admin, you can still create a host but the C-DEngine will revert back to a user-mode web server that has less performance.
2) Name the project "cdeHostNetCore"
>After a short time you will see a small project with a program.cs and the C-DEngine icon.
3) Open the program.cs and make one change in line 36 by creating a new GUID with the GuidTool shipping with Visual Studio and pasting the guid in the line.
4) run the project and look at the console output
>If you see any error in the console, you most likely have a port conflict and something else already uses port 80. If so change line 39 and 43 to use any open port. Our recommendation is to use ports higher than 8700.

>If you are running VS as admin, you can use the same port for HTTP (MyStationPort) and WebSockets (MyStationWSPort). The http.sys can share the same port for http and websockets. If you are not running under admin, you have to specify a different port for the WebSockets.

The source of the host is fairly self explanatory but I want to point out a couple details:
#### DontVerifyTrust=true
The C-DEngine can ensure that only plugins are loaded that have the proper code signing certificate. Code signing requires a certificate and some extra build steps. For simplicity you can turn Code Signing off during development by setting "DontVerifyTrust=true". 
>If you are interested in Code Signing, please contact us and we will write more about it

### UseRandomDeviceID=true
For development it can help to always start the host fresh without storing any information on your Harddrive. This switch turns off all state management and gives the host a new DeviceID with every new start. If you keep this as the default (false) the DeviceID and other state information are saved in a folder under your bin file: "ClientBin/cache". You can delete this folder to start fresh with the host again.

### ...meanwhile...
While the host is running, you can enter "b" in the console and a Brower will open with the User Interface of the node. As we have no NMI Runtime installed, yet, the browser will only show:

**Resource http://yourpc:yourport/lnmi not found***
Right now the C-DEngine is running in headless mode. We will add the NMI in the next step.

## Adding the NMI Plugin to the Host
Adding the NMI Runtime to the project is as easy as adding a NuGet:
1) Open the Nuget Manager and browser for "NMI"
You will find the CDMyNMIHtml5RT package - just add it to the project. You don't have to do anything to your program.cs - just run it again

If you now enter "b" in the console the browser is showing a login screen.
By default the program.cs creates a random Scope for the host (in line 98).
You can create a different Scope for easier development as the Scope will change every time you restart the host.

You will find the Scope ID in the console in the second line of the output:

```Scope ID: XXXXXXXX```

The Scope ID is a Base32 string with up to 8 characters.

2) Enter the Scope ID in the Login dialog and click on "Set Security ID"

>You will now see the minimalist NMI (Natural Machine Interface) of the Host. 
3) Select NMI Admin and you can see a variety of options.
***TODO: Explain NMI Admin here***

TO

## Adding plugins from our Plugins Depot
The cdePlugins depot on GitHub has several plugins we can now add to the project. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDY1OTkzNzE4LC01MTcwMjkwMDAsNjA0Nz
M5ODk4LC00Mjc3Mzk4MDYsMjk5MTE1OTYxLDEwMDcwNjMyMDJd
fQ==
-->