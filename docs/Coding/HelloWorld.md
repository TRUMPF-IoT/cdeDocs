# Welcome to the C-DEngine

## Rapid Application Development Platform for distributed Industrial IoT Solutions

This Getting Started Guide walks you through the development of C# source code for a C-DEngine test hose, C-DEngine service running in a simple plugin, the routing of network traffic through the Cloud.

+ The C-DEngine API reference documentation can be found at http://www.C-Labs.com/docu

Have fun and be creative!


# Getting Started

## Creating your first IoT Solution

C-DEngine IoT solution might contain up to four different elements. 
1)	A Host Application – An executable program that creates the main operating system process, then starts the C-DEngine. This could be an existing application being connected to the IoT. 
2)	One or more plugins – these are dynamic link libraries (DLLs) that contain the business logic and device access code for each device type that wants to participate in the IoT solution. Plugins extend the C-DEngine, and can be used for things like device-specific code or protocol-specific code. 
3)	Web Browser – enables access to the dashboard created by the host application. No additional coding is required for this component. 
4)	Cloud Relay – an optional component to enable access from mobile devices. This evaluation version of the C-DEngine uses a shared cloud-relay node. 

### Creating a C-DEngine Host-Relay Application
The C-DEngine is a DLL that manages the IoT communication and user interface elements of "Things". It must be hosted in an operating system process. Possible process types include a Windows Service, a Windows Forms application, a console application, or even an ASP.NET Web application.
One node can relay information to any other node. The host we create here can relay information from locally attached devices, out to a cloud node that than passes the data on to mobile device or perhaps to business applications. Local mobile phones can connect directly to this host-node without the need to connect to the cloud/internet.


### A Console Host in 4 steps:
For this Getting Started exercise, we create a console application. 

1) Open Visual Studio 2019 and create a new project of type "Console Application". 
+ We recommend running Visual Studio as Administrator that the host can use the lower level Http.sys functionality for the web server and WebSockets
+ You can use .NET Core or .NET 4.6.2 or higher

2) Add a reference either to our C-DEngine nuget package or if you have cloned our depot, add a reference to the C-DEngine directly

If you want to compile the C-DEngine you need to add a couple references from our depot:
+ Utilities/cdeNewtonsoft.JSON (the Newtonsoft library is linked into the C-DEngine to make sure our JSON serialization is not compromised by plugins that might bring their own - possibly older version of Newtonsoft)
+ cdeUpdater (the updater is used to update the C-DEngine and plugins)
+ Utilities/cdeZlib (our shared project of the ZLib required by the cdeUpdater. This component might be removed soon)

3) Add a reference to the CDMyNMIHtmlRT nuget or the project from our depot

4) in the main function of your program.cs add this:

```
   TheScopeManager.SetApplicationID("]@wuhZrj2jnz:pOORnEZv523ANgRZ@]hWif;c:[HFNu?");
   TheBaseApplication BaseApp = new TheBaseApplication();
   TheBaseAssets.MyServiceHostInfo = new TheServiceHostInfo(cdeHostType.Application) //Tells the C-DEngine what host type is used.
   {
       ApplicationName = "Test-Relay", //Friendly Name of Application
       cdeMID = TheCommonUtils.CGuid("<great a new guid here>"),
       Title = "Test-Relay",
       DebugLevel = eDEBUG_LEVELS.OFF,
       ApplicationTitle = "Test-Relay Portal",
   };
   if (!BaseApp.StartBaseApplication(null, null))
   {
       return; //start failed...handle it gracefully
   }
   TheScopeManager.SetScopeIDFromEasyID("2312");    //Change this to a code you like

   var t = Console.KeyAvailable;   //Try to see if we have access to the console
   while (true)
   {
       while (Console.KeyAvailable == false && TheBaseAssets.MasterSwitch)
       {
           Thread.Sleep(250); // Loop until input is entered.
       }
       if (!TheBaseAssets.MasterSwitch)
       {
           //Leaving key pressed loop because MasterSwitch was false
           break;
       }
       ConsoleKeyInfo key = Console.ReadKey();
       if (key.Key == ConsoleKey.Escape)
       {
           // Leaving key pressed loop because Escape key was pressed
           break;
       }
   }
   BaseApp.Shutdown(false, true); //shutdown the engine properly
```

> The same code works for any other host. Important is that you set the corresponding cdeHostType for the different host variations


#### Done!


Now run your project. It will not show much output in the console but if you turn the DebugLevel up you will see much more diagnostic output

If StartBaseApplication fails or you will see an error that the default Web Port (80) is already used, you might have to change the port.

Add this line after "DebugLevel" in TheServiceHostInfo (refered to as "HSI"):
```
                MyStationPort=8744,
                MyStationWSPort=8744,
```
The first setting is the HTTP Port, the second setting the WebSockets port. If you dont specify a WebSockets port, the C-DEngine will not use WebSockets.
Only on Windows 8 and higher (when running VS under administrator) or Linux you can set the WebSocket Port to the same port as the http Port
Otherwise you will have to use a different port for WS.

### Browsing the User Interface...

+ Now open your favorite browser (we recommend Chrome or Edge) and navigate to "http://localhost:(the Port you sepcified)/nmi"
+ You should see a login dialog asking for a scope id. 
+ Enter "2312" (or whatever you change the code to)

#### Bam! You see the user interface - the NMI (Natural Machine Interface)

Look into the bin/debug folder of your project and you will see that the Host has created a subfolder called "ClientBin/Cache" with some files in it.
These files contain some settings, cache information and other important data

> If you want to start fresh, you can delete the ClientBin folder. But all stored data will be lost.

### Connecting to the cloud

Connecting to the cloud is as easy as adding one line of code (again under the DebugLevel in the HSI):

```
ServiceRoute="wss://cloud.c-labs.com",
```

Now restart the host. If you login to the NMI now, you will see some more tiles coming from the cloud.

You can also go to: http://cloud.c-labs.com/nmi

and login with your 2312 code (or your change) and see your local relay in the cloud.

> The C-DEngine Clouds do not require any "registration" or other sign up. This is a unique feature of the C-DEngine IoT platform!
You can also create and host your own clouds. How we will document very soon...

For now there is not much to see, yet...we have to write some plugins first

> You can also find a lot of sample plugins in our "CDE-Plugins" Depot


## Creating a Plugin in 4 steps

Plugins contain business logic, such as 

+ Connectivity (drivers) to machines, sensors and devices
+ connectivity to services - local or cloud based
+ algorithms for data pre-(edge)-processing 
+ User interface code for dashboards 

1) Create a new class library (.NET Standard) in your solution and name it "CDMyTestPlugin"

> it is important that the plugin starts with "CDMy" (spoken "seed my ..."). The C-DEngine will try to load plugins with this prefix automatically

2) Add a reference to the C-DEngine as you did for the host

3) in the created "Class.cs" exchange all the code with these lines:

```
using nsCDEngine.Engines;
using nsCDEngine.Engines.NMIService;
using nsCDEngine.Engines.ThingService;
using nsCDEngine.ViewModels;
using System;
using System.Collections.Generic;

namespace CDMyTestPlugin
{
    public class eThings : TheDeviceTypeEnum
    {
        public const string MyThing = "Thing Device Type";
    }

    public class TestService : ThePluginBase
    {
        public override void InitEngineAssets(IBaseEngine pBase)
        {
            base.InitEngineAssets(pBase);
            MyBaseEngine.SetFriendlyName("My first plugin");
            MyBaseEngine.SetEngineID(new Guid("<Great a Guid and paste in here>"));
            MyBaseEngine.SetPluginInfo("This service is my first", 0, null, "toplogo-150.png", "C-Labs", "http://www.c-labs.com", new List<string>() { });
        }

        public override bool CreateUX()
        {
            if (!mIsUXInitCalled)
            {
                mIsUXInitCalled = true;
                TheNMIEngine.AddDashboard(MyBaseThing, new TheDashboardInfo(MyBaseEngine, "My Plugin"));
                TheNMIEngine.CreateEngineForms(MyBaseThing, TheThing.GetSafeThingGuid(MyBaseThing, "MYPLUGIN"), "Things of my Plugin", null, 20, 0x0F, 0xF0, TheNMIEngine.GetNodeForCategory(), "REFFRESHME", false, new eThings());
                TheNMIEngine.RegisterEngine(MyBaseEngine);      //Registers this engine and its "SmartPage" with the System
                mIsUXInitialized = true;
            }
            return true;
        }
    }
}
```

4) add your new library as a reference to your Host 

### Done!

Start your host again, login to the NMI and you can see a new Tile for your plugin,
Connect to https://cloud.c-labs.com/nmi and check out the plugin NMI in the cloud. 

There is soo much more to explore and we will post more documenation and YouTube videos in the next couple weeks. Stay tuned!

Please let us know if you have any feedback, post issues here in the issues section and visit our website www.c-labs.com.
You can also contact use at info@C-Labs.com or support@C-Labs.com.

Have fun greating REAL(!) IoT systems.

Your C-Labs Team
