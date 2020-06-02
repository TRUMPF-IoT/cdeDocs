# C-DEngine Technology Tutorial
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

To stop the host, simple press ESC in the console. If you have the browser open you will see that you get immediately logged out of the NMI when the host stops.

## Adding plugins from our Plugins Depot
The cdePlugins depot on GitHub has several plugins we can now add to the project. We are working on publishing all plugins as NuGets as well. You can Browser for Compatible plugins by searching for "CDMy" in the NuGet Package Manager.

>C-DEngine automatically recognizes plugins by the prefix "CDMy" or "C-DMy". Therefore its very simple to add new plugins to NuGet and the bin folder

First we are adding the CDMyNetwork plugin.

If you want to use the source...
1) Clone the "cdePlugins" depot to your local drive
2) Add the project "src/093 - CDMyNetwork"
3) Add a reference to this new project to your Host Project
4) Run the solution (Make sure your starting project is the Host)

If you now login to your NMI, you will see the NMI for the Network Plugin.
Lets create some "Digital Twins" in the network plugin.

In the C-DEngine a "Digital Twin" is a "Thing" managed by the C-DEngine.
A "Thing" (or the base class "TheBaseThing") is owned by a plugin and represents the state and events of a physical device. 
In case of the network plugins, we are creating digital twins of ping-able network end points such as the DNS Server of Google (at 8.8.8.8).

1) To add add a digital (endpoint) twin click on the "Devices/Network Status" tile in the NMI
2) Then click on "Add new Network Service"
> A wizard will appear. Wizards are part of the plugin and help to create new instances of digital twins
3) In the wizard set the following values:
```
Name Your Service: "Google DNS"
Service Type: Select "Ping Service"
Address: "8.8.8.8:
```
Once you click finish, you see a new tile in the NMI for the Google DNS
If you click on it, you see the device status:
***IMAGE OF NMI HERE***
You can see the "Value" updating frequently. Its shows the roundtrip time of a ping from your host PC to the Google DNS.
If you open up the "Connectivity" Group by clicking it, you can see that "Auto Connect" and "Is Connected" are selected. you can also see and change the address of the ping as well as change Ping parameter if you open that group.

>***Important Note***: If you still have "UseRandomDeviceID=true" set, your new digital twin will be gone after you restart your host. We do recommend that you change this to "false" now for the remainder of the tutorial.

Lets have a look where this "Thing" (Digital Twin) was created.

1) Click on the C-DEngine logo in the top left and then the little home icon.
This brings you back to the main portal
2) Now click on the NMI Admin and then "Thing Registry" tiles
3) in the top right corner of the table click the little "Refresh" icon and you will see all "Things" your host is currently managing.

In the "Friendly Name" column you will find your "Google DNS" thing.
To the left are three buttons:
|Button  |Action  |
|--|--|
|First button| Jump to the Thing's NMI  |
|Second button| This button allows you to download the JSON description of the Thing |
|Third button|  This button gives you some more options to analyse your Thing. Especially interesting is the "All Properties..." Group. If you open it, you will find all properties a Thing has. |

Things in the C-DEngine only consists of Properties! All of which are stored in the ThingRegistry (class name: TheThingRegistry). Developer can access things via TheThingRegistry APIs.

***DESCRIBE MORE OPTIONS ON THINGS***
## Connecting the Host to the Cloud Relay

Solutions with the C-DEngine can run completely on premises without the need for an internet connection. 
But if an internet connection is available, the C-DEngine can easily be connected to a "Cloud Relay" allowing access to the local NMI via cloud connected devices.  
This is as simple as adding one line of code to our hosts program.cs:

1) in the program.cs add the following line after line 54:

```
ServiceRoute="wss://cloud.c-labs.com"
```

Now start your host project again. If you login to the local NMI you will now see a couple more tiles under the category "Diagnostics" and a new category "Node: C-Labs Cloud" is showing.

You can also login to the Cloud now by going to 
```
https://Cloud.C-Labs.com/nmi
```
You will see the same login dialog as you had locally. 
After you enter your Scope ID you will see the same NMI as on the local node.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTczNzc0Njg1LDE5OTU2NjY3LC0xOTIzOD
U4Nzk1LC05MjY3ODAyNywtMTA3MTQ4ODksMjExODE5Nzg3OSwx
ODc2NTY0ODMzLDE4OTkwNjU0OTAsLTUxNzAyOTAwMCw2MDQ3Mz
k4OTgsLTQyNzczOTgwNiwyOTkxMTU5NjEsMTAwNzA2MzIwMl19

-->