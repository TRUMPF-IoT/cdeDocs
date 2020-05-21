# C-DEngine - Change History

## Version 4.303 (March 2020 - LTSB)
+ new cdeMeshInfo.aspx for information on a given mesh by NodeID
+ new and improved cdeStatus.aspx & .json with more detailed information about node status
+ new Security enhancements
+ Removal of retired APIs for:
  + cdeTelnet: Now a shared project if required in plugins
  + cdeRSS: Now a shared project if required in plugins
  + Update option via USB Stick - retired per security guidelines
  + Removal of Windows Event Log support - can be implemented in plugin if needed
  + Removal of Charts Factory from Core - can be implemented in plugins if needed
+ New API for fast Plugin and Thing creation (ThePluginBase and TheThingBase) 
  + Updates of the SDK templates using these base classes
+ New EngineState.LastMessage access for plugins - visible in cdeStatus.aspx
+ New automatic versioning system for Build process
+ Improved NMI Resource fetching across a mesh

## Version 4.301 (January 2020)

+ Option to disable plugins from Authorized sources (Provisioning Service)
+ New platform dependent Manifest files in ThePluginInfo (syntax: "@platform@filename")
+ Support for Pre-Flight security "OPTIONS" coming from certain browser
+ new KPI system to add new KPIs at any time (see TheCDEKPIs class)
+ Improvements to Discovery System for auto-connecting mesh nodes (requires CDMyNetscanner plugin)
+ Plugin Load order improvements: Loading Internal Plugins then NMI Extension and Skin Plugins first
#### NMI Improvements
+ Support for PIN login to NMI (by default off. Needs to be enabled in app settings)
+ Dymamic Tile Sizes to optimize better for different mobile platforms
+ Geo Location support for Browser and Relays (by default off. Needs to be enabled in app settings)
+ Improved WebWorker for NMI sessions across browser tabs
#### Retired APIs
+ cdeHttpServer (Mini TCP based Web Server that was not in use by core or plugins)

## Version 4.212.0-Beta
- Client Certificate support

## Version 4.206.2

 - Security fixes: SSL cert validation, code signature validation and reporting
 
## Version 4.206.1

 - Stabilisation and long term support branch
 - NMI allows loading of any screen via "TransitToScreen" from 3rd party UX environments

## Version 4.205.1

- Dropped support for NMI 4.0 - please use NMI 4.1 going forward

## Version 4.205.0

- New Support for Mesh Manager Provisioning Service
- New Sensor Model: Configuration management, sensor container/consumer/provider management, sensor pipeline management.
- Default fallback to "Device" if the MyStationPort is already used. Can be turned off to fail the executable if "DontFallbackToDevice=true" is set
- New "TheCDEEngineHost.RegisterPubSubTopic" to register any C-DEngine Publication topic (similar to REgisterNewMiniRelay but returns TheBaseEngine
- New Memory Optimization Setting "IsMemoryOptimized". Will limit the SystemLog to 10 entries (if you need more turn on the Text based log) and limits the Send-Buffers to 1024 (normally depending on the SenderType)
- New NMI Optiomizations
  - Attention! In the past buttons in Tables were not enforcing the Flag=2|0 correctly. All buttons in tables must have flag=2 set, if the button is pressable (otherwise its disabled)
- New NMIEngine function: RegisterSyncedTransform("resourcename","UserID")
  - if UserID is Guid.Empty the transform will be applied to all user that have the necessary permission
  - if userID is a guid of a user, the transform is only applied to the user
  - The resource is synced with the cloud but only applied to users of the node that own the resource

## Version 4.203.2

- SDK VSIX Template support for Visual Studion 2019

## Version 4.203.1 -- March 13th, 2019

- Bug fixes and version corrections

## Version 4.203.0 -- March 10th, 2019

- NMI Improvements for better readability of complex forms
- New Wizard APIs, templates and Popups
- General Stabilization


## Version 4.202.3 -- February 27th, 2019

- TheCommRequestResponse: Add overloads without originator thing
- TheCommonUtils.GenerateFinalStr: add option to specify default value. Syntax: `%APP:<settingName>:<default>`%
- Bug fixes

## Version 4.202.1 -- February 18th, 2019

- Historian: Fix race condition resulting in unnecessary duplicate update delivery with multiple consumers (regression)

## Version 4.202.0 -- February 10th, 2019

- New NMI enhancements
  - ThingPicker, PropertyPicker and Combo Lookup improvements
  - New Template and Wizard APIs
  - New option to set a template as a popup (Preview! Will be finalized in 4.203)
  - Improved Form Post handling: Button clicks are automatically delayed by 200ms in the NMI
  - NMI Telegram batching in Browsers
  - Drag&Drop support on tables
- New Provisioning settings for Provisioning Service. Detailed explanation will be in the Mesh Manager Provisioning service documentation
- TheThing.SetStatus convenience method to set StatusLevel and LastMessage in one call.
- .Net attribute-based engine asset registration
- Discovery of all device types by capability (for cooperating plug-ins): TheThingRegistry.GetAllDeviceTypesByCap, IBaseEngine.SetDeviceTypes
- Bug fixes

## Version 4.112.0 -- December 21st, 2018

- Bug fixes

## Version 4.111.0 -- November 14th, 2018

- Rename thing update stream files from `ThingHistory<cdeMid>` to `ThingUpdateStream<cdeMid>`
- Delete easyscope from app.config on first run
- NMI 4.1
- Bug fixes

- New NMI 4.1
  - The old 4.0 version of the NMI is still included as “CDMyNMIRT5-40.dll”
  - New APIs and many new features. We continue to improve on it.
  - New ThingPicker and PropertyPicker
  - Move back to a complete passive NMI Model! Subscriptions on “OnUpdateNames” are executed only if a browser/subscriber exists and requests the Form Resource
  - New Wizard APIs in TheNMIEngine
  - Allows to easily and fast create wizards for your plugins
- Many improvements for speed and footprint of our cache folder 
  - Optimized ThingRegistry Save to cache
  - cdeE&10!=0 on a Thing Property will prevent the property from being serialized (not stored to disk – not sent to the NMI by default)
- New “LogOnly” setting in app.config
  - Allows to specify a list of ENG for logging. Only these ENG will be written to the SystemLog. 
    NOTE: ONLY USE FOR DEBUGGING as you might miss important log entries

## Version 4.109.4 -- October 1st, 2018

- TheCommonUtils.cdeRunTaskChainAsync: Runs a sequence of asynchronous tasks (task continuations or a method that uses the C# async keyword) and lets the caller wait for the entire chain to finish. The call returns immediately and not when the first continuation is hit.
- TheCommonUtils.CTimeSpan: allows storage of TimeSpan instances as a (culture invariant) string in default .Net TimeSpan format.
- UseSyslogQueue setting: uses a queue for SysLog.WriteToLog instead of creating a task for every log entry
- Bug fixes

## Version 4.109.2 – September 15th, 2018

- **New NMI 4.1 Preview**

    -   The SDK contains the new NMI 4.1 as a preview in the NET45 folder.
        Please experiment with it and test your scenarios. You can also copy the
        DLL in our products (AXOOM Gate, Machine Monitor, Home-Relay and
        Factory-Relay) but you have to remove the CDMyHTM5RT.DLL as both version
        in the same relay will create conflicts. **Do NOT shop this DLL to any
        customers! There is no GO-LIVE license with this preview**.

-   Historian now supports two new options in TheHistorianParameters:

    -   ReportInitialValues: returns the current values for the properties at
        the time of registration before any property updates

    -   IgnoreExistingHistory: returns only updates that arrive after the
        registration

## Version 4.108.0 – August 27th, 2018

- Enhancements

  - initial connection to a C-DEngine relay will now delay the result
        by 200ms. This will delay brute force attacks significantly

  - All Web Outputs beside the NMI can now be protected with a
        “SecurityToken”. This token must be added to the App.Config:

    ```XML
    <add key="StatusToken" value="CreateSomeRandomTokenHere"/>
    ```

If the token does not in the app.config, access is allowed to all pages. The
following pages will only show if the user is calling the page with ?\<token\>
in the url:

-   cdeSetPara.aspx – allows to set some of the App.Config values during runtime

-   cdeDiag.aspx – shows diagnostics information of the node (expensive! It will
    enumerate over threads and processes and can delay execution of code)

-   cdeHSI.aspx – shows all HostServiceInfo settings (Main Settings Table of the
    C-DEngine)

-   cdeKPIs.aspx – shows all known KPIs of the C-DEngine

-   cdeStatus.aspx and .json – gives a comprehensive status of the node

-   log.aspx – shows the current system log of the node

-   evtlog.rss – RSS feed of the event log of the node (created by the rules
    engine)

-   syslog.rss – RSS feed of the system log of the node

-   **IMPORTANT CHAGE: cdeN was moved from TheMetaDataBase to TheDataBase**

    -   Since TheMetaDataBase is derived from TheDataBase the change should be
        minimal to plugin code. Reasoning:

        -   The cdeN field contains the Node ID (DeviceID) of the node that
            TheDataBase instance was created. This is required for better
            tracking of records in TheStorageMirror and the NMI. Since
            TheStorageMirror only requires classes to be derived from
            TheDataBase, the Storage Mirror often did not know what node these
            records were on. This has been mitigated by adding cdeN to
            TheDataBase. Same for some NMI control information that need better
            tracking back to its owning node.

    -   Here are some implications:

        -   The StorageMirrorCache Table Name is derived from a fingerprint
            calculated from the class that is to be stored. Since cdeN is now
            moving up to a base class, the fingerprint can change. Better
            practice is to set the CacheTableName on the StorageMirror class
            manually.

        -   Messages of classes derived from TheDataBase are now longer (one
            extra Guid Field)

-   **IStorageService Interface APIs have been changed!**

    -   All previous interfaces with “Edge” inside have been extended by a
        “TableName”. This will allow the underlying database technology to use a
        predefined table name. If the parameter is set to “null” the call works
        as before.

    -   So far there are no known plugins that have used this interface
        therefore the impact of this change should not affect any application

-   New method in IStorageServce interface: “InitEdgeStore()”

    -   This method is used to initialize an underlying Database.

    -   A detailed documentation of the IStorageService will be coming with a
        future release

-   New “Pin-able Licenses” against XCopy protection

    -   In a CDEL file, we can indicate that any activation keys for that
        license is to be pinned to a specific machine (i.e:
        CDEnginePinned.cdel).

    -   If this license file is found on first startup, the C-DEngine generates
        the DeviceID as a hash of the MAC address of the primary network card.

    -   On subsequent startups/license checks, the C-DEngine validates that the
        DeviceID still matches the MAC address.

    -   On license failure, the C-DEngine shuts down after 1 hour (or can be
        configured via app.config to shut down immediately).

    -   If you require such a pin-able license, please contact C-Labs.

## Version 4.106.0 – June 28th, 2018

-   Security Enhancements

    -   ScopeID and ApplicationID are no longer stored in RAM after used for
        Mesh Seeding. Retrieving of the ScopeID of a node is no longer possible

    -   Passwords are now stored only with their hashes and cannot be recovered

-   Internal Performance tuning

    -   Communication flow tuning - Most visible on cloud-relays (4Million
        Telegrams per second/ 1500 relays connected to one cloud at 15% CPU)

    -   Storage tuning – introduction of Reader/WriterLock in Storage System

-   New /MYISBCONNECT to connect to a relay from a 3rd party application

    -   Requires the App.config switch “AllowRemoteISBConnect” to be set to
        true. This switch cannot be changed during runtime

    -   MYISBCONNECT will allow access to a mesh like a browser would get. The
        node that MYSIBCONNECT was called on is the security proxy of the
        connected application.

    -   Use this for injecting telegrams into a mesh or listen to special
        messages from the mesh

    -   The return of MYISBCONNCET is a JSON telegram of TheISBConnect class. It
        contains the ports, URL and other information to connect properly to the
        mesh

## Version 4.105.0 – May 28th, 2018

-   New SDK Base Class Template for an Energy Device “cdeEnergyThingBase.cs”.
    Allows to easy and fast create Energy Meters

-   Internal refactoring for better code readability and maintainability

-   New setting for blacklisting nodeIDs:

    -   Add “NodeBlacklist” to App.Config and in the value put a list of all
        blacklisted NodeIDs separated with semicolon

-   WebSockets tuning, encryption tuning, heartbeat tuning for high performance
    nodes

-   NMI Changes:

    -   AddStatusBlock() now uses ACL=0xFE for access to DeviceStatus. Operators
        and anonymous can no longer see the device Status


## Version 4.104.0 -- April 2nd, 2018

-   New TheStorageMirrorParameters allowing to set important StorageMirror parameter

-   Internal improvements for TheStorageMirror cache store

-   Internal NMI Fixes

-   General stability improvements

-   New "UseEasySCopy16" HSI setting (Preview)

    -   If set, the ScopeID will use 16 characters (default is 8 characters).

    -   **WARNING: All nodes in a mesh have to be switched to 16 characters as the new scope ID is not compatible with the old 8 characters ScopeID. This includes any Cloud Node. If and when the CDE will complete move to 16characters is currently not defined.**

## Version 4.102 -- February 20th, 2018

-   Inplace update of SDK. No need to uninstall older version

-   Improved logging

-   New PropertyTypeChanged event on TheThing and cdeP

-   NMI Improvements

    -   Saving of user preferences for CollapsibleGroups

    -   Runtime Form and control override options

    -   New form refresh option on top of forms next to pin icons -- Data (left click) and Data+Meta (right click)

-   Various internal bug fixe

## Version 4.1010 -- January 8th, 2018

-   New Version interpretation going to Semantic Versioning with Date Encoding:

    -   \<MajorVersion\>.\<YearSince2017\>\<MonthOfYear\>.\<BuildNumber\>

    -   First version of 2018: 4.101.0 (V4, 1=2018, 01=January, 0=Initial Drop)

-   NMI Improvements

    -   Saving and restoring Views now working with Things even if the dashboard is not "ForceLoaded"

    -   User can save changes to "Thing" Forms. In this release only opened/closed as well as expnded/contracted Collapsible Groups.

    -   NMI\_GET\_DATA optimization to avoid double loading of forms

    -   Replacement of Pin, Unpin, DivUp and DivDown PNGs with FontAwesome.

## Version 4.0120 -- December 6th, 2017

-   Supporting platforms now:

    -   .NET Framework 4.6.2

    -   .NET Standard 2.0 (for all platforms supporting .NET Standard 2.0 like Linux, Xamarin, UWP and Docker)

-   Dropped support for platform specific versions (UWP, Android, IOS and .NET Core)

-   Several NMI reliability fixes for SmartPages, ComboBox (PropertyPicker), Chart Control loading

-   New NMI "Node Lost Blurring".

    -   In a bigger mesh we now blur screens and controls of nodes that are no longer reachable. In the past device forms and screens were still visible and "Active" even if the corresponding node was no longer reachable on the mesh. The user got the wrong impression that the data on the screen is still accurate. Starting 4.012 the NMI will blur all resources from nodes no longer reachable in the mesh.

-   New NMI Event in JavaScript is a theme switch occurred

-   New option to turn on Log Output in GELF Format (UseGELFLoggingFormat).

    -   See Graylog.com for details on GELF

-   General performance improvements across the communication of the C-DEngine

-   Improvements on cloud connect, reconnect and heartbeat management

-   New AdHoc Scopes for unscoped cloudnodes (see [www.ink2.me](http://www.ink2.me) for an example).

    -   Cloudnodes can now allow AdHoc scopes that allow users to set a scope in the browser. If two browsers share the same scope, they can "talk" through the cloud. This allows for many browser-to-browser scenarios. For security reasons this is ONLY allowed on Cloud Nodes with no dedicated scope.

-   Allow environment variables to override app.config settings AllowEnvironmentVarsToOverrideConfig (default is false)

-   New CDE\_PING -- CDE\_PONG behavior.

    -   Both are handled in TheBaseEngine and services can CDE\_PING a specific service on a node. The PLS of the CDE\_PING will be returned by the CDE\_PONG in its PLS with an additional ;BaseEngine.LastMessage. To check on general availability of a node, send CDE\_PING to "ContentService"

-   Pre-Shutdown event

    -   This allows plugins to deal with a pending shutdown of the node there are running on. The Event is fired on the TheCDEngine.MyContentEngine. Use RegisterEvent(eEngineEvents.PreShutdown, ...) to register for the event. Plugins can set "MyServiceHostInfo.PreShutdownDelay" (default is zero) to set a time, the CDE will wait until executing the shutdown. Once a shutdown is initiated, TheREST will no longer accept any POST or GET calls, the communication will be shutdown and all plugins will get the "Shutdown" event.

-   New Platform identifies for MyServiceHostInfo.cdePlatform:

    -   X32\_V3 = 1, //.NET 3.5 32 Bit only

    -   X64\_V3 = 2, //.NET 4.5 64 Bit

    -   ANY\_V3 = 3, //retired

    -   ARM\_V3 = 4, //retired

    -   MONO\_V3 = 5, //retired

    -   METRO\_V81 = 6, //retired

    -   SILVERLIGHT\_V5 = 7, //retired

    -   WPHONE\_V8 = 8, //retired

    -   IOS\_V6 = 9, //retired

    -   ANDROID\_V4 = 10, //retired

    -   ECLR\_V1 = 11, //PxC ECLR -- not currently supported

    -   NETCORE\_V20=12, //Docker/Linux

    -   NETSTD\_V20=13, //any unknown .NETstd platform

    -   NETV4\_32=14, //.NET 4.0 32 Bit

    -   NETV4\_64=15, //.NET 4.0 64 Bit

    -   X32\_V4 = 16, //.NET 4.5 32 Bit

-   Updated Code Signing Certificate valid until December 2019

## Version 4.0100 -- October 25, 2017

-   Enhanced Portability

    -   A single C-DEngine DLL, built on .NET Standard, enables support for these platforms:

        -   .NET Framework 4.6.2

        -   .NET Core 2.0 (In Preview!)

        -   Xamarin Forms (Android, iOS)

        -   Universal Windows Platform (UWP)

    -   Because of the above support, there are no longer separate C-DEngine DLLs for these platforms:

        -   Android

        -   iOS

        -   Mono

        -   Universal Windows Platform (UWP)

-   Docker Support (in Preview)

    -   Requires Docker version 17 and higher

    -   C-DEngine support is based on the .NET Core 2.0

-   Performance Tuning - C-DEngine 4.010 provides faster communication, fewer locks and bottlenecks.

-   Adding C-DEngine Key Performance Indicator Support

    -   Enable with Application Host switch EnableKPIs=true

    -   Setting value dynamically enables turning on / turning off for targeted performance tuning

    -   When enabled, application host track a set of predefined indicators as properties

-   User-Interface Update

    -   Improved plugin dashboard layout

        -   Instance items grouped together as the first group on a dashboard

        -   New function to enable improved layout in **AddAboutButton4** function

        -   New function for group break: **TheNMIEngine.AtTileBreak**

    -   Refresh to user-interface to add new look and feel in a \"standard form\"

        -   New function: **AddStandardForm** - helper function to create new redesigned form look and feel. (Convenience function for two functions: **AddForm**, **AddFormToThingUX**.)

        -   Definition of a \"standard form\" (and new API, see below) to improve consistency in user-interface. This includes a new collapsible group as the first item (field order = 1), which is the best place for your controls that you wish to have appear in the collapsible group.

        -   Three things created: form, dashboard icon, collapsible group header -- returned as dictionary of meta base

    -   Tables - improved consistency in column layout

        -   New function: **AddCommonTableColumns** - Details button, friendly name, status, device type, and delete button

    -   Enhanced support for mobile, tablet & desktop to use available browser screen space.

-   Enhanced Group Control

    -   Header redesigned for improved visibility, more standard expand / collapse buttons

    -   Adds horizontal collapsing

-   ComboBox control enhancement

    -   Add support for \"write-once\" -- after first input, control becomes read-only

    -   Enable by setting **WriteOnce=true**

-   Updated Visual Studio Templates

    -   All templates now use new standard form function, **AddStandardForm**

    -   All templates now demonstrate new improved dashboard layout in function **AddAboutButton4**

-   Adds Support for Remote Provisioning, Management, and Updating of Nodes

    -   There are two parts to handling plugin updates: (1) Downloading plugin update, and (2) Applying the update and rebooting the node. Two new switches allow control of each step.

        -   **AllowAutoUpdate** switch -- when true, check for availability of plugin updates and download them when they are available.

        -   **AllowRemoteAdministration** -- when true, applies plugin updates and reboots the node.

-   Enhanced configuration and deployment support

    -   Optional use of environment variables for **HostServiceInfo** settings

    -   Requires setting **AllowEnvironmentVars**=true in **HostServiceInfo**

-   UPnP moved from core to new network scanner plugins

    -   New functions exposed in **ICommonDisco** interface to enable the creation of custom discovery protocols such as ZeroCon, Bonjour, mDNS

    -   New API functions:

        -   **RegisterScanner(ICDESniffer)**

        -   **UnregisterScanner()**

        -   **RegisterDISCOService(ICDEDiscoService)**

        -   **UnregisterDISCOService()**

-   Smaller C-DEngine Footprint

    -   Smaller core

    -   UPnP support moved to its own plugins

    -   Cloud services plugin redeployed as six different communication plugins.

-   Free and Open Source Schedule (FOSS)

    -   A single place for all open source in SDK

    -   See Appendix D

### Version 4.0090 -- September 6, 2017

-   Accumulated fixes since 4.0080 to 4.0083

-   Platform Portability

    -   C-DEngine Preview Support for .NET Core. Eventually this will replace the C-DEngine for IOS, Android, Portable, Mono and UWP.

    -   New HostServiceInfo setting (DisableRSAToBrowser) for platforms not supporting our standard RSA Exported Public Keys (Core, UWP)

-   Optimizations

    -   Improvements how the ThingRegistry is stored (direct flush to disk vs in Memory JSON Conversion) =\> should be more gracefully to memory consumption overall

-   Engine Interface API Improvement

    -   New "GetBaseEngine()" Interface in IBaseEngine. Allows to get TheBaseEngine on the interface

-   System Startup Changes

    -   Plugin initialization behavior:

        -   Old: InitEngineAssets and right after RegisterEngine on each plugin

        -   New: First all InitEngineAssets for all plugins -- then RegisterEngine on all plugins. This was the plan all along but was not implemented until now.

    -   Plugin initialization performance improvement

        -   Optional ability for initialization functions to run asynchronously

            -   Enabled by new plugin initialization behavior (described above).

            -   All plugin initialization functions (Init and CreateUX function) run asynchronously.

            -   Plugins that misbehave in initialization functions only do not cause delay for other plugins.

            -   As before, plugins cannot rely on the startup order of other plugins!

            -   Enable with \"syncEngineStartup\" in HostServiceInfo

-   Bug Fix

    -   Correct Handling of DefHomePage field in HostServiceInfo. A small bug prevented the setting of a Default Home Page for the Relays. Especially for Cloud Relays this could lead to empty root-page requests.

-   Browser / Javascript

    -   Update to latest version of TypeScript, version 2.5.2

## Version 4.004 -- April 4th, 2017

-   ADFS/OAuth2 Interfaces ready. A new Plugin "OpenID" can be used to connect to an ADFS/OAuth2 login provider

-   Thing Historian improvements

-   New "TheISBConnect" class to create custom scoped ISB Connections (Multi-Scoping and Federation)

### NMI Enhancements:

-   NMI Performance improvements: (1) dynamic loading of screens, and (2) Mini-Subscriptions for Fields and ThingsIDs on First Node to reduce traffic to JavaScript nodes

-   Clock on Larger screens (all but Mobile)

-   Full support for all Windows 10 platforms (Desktop, Mobile, HoloLens, X-Box, and IoT). "NMI Viewer" app is ready for the Windows Store

-   New "PlatformBag" on Forms (TheFormInfo) and Fields (TheFieldInfo) allows to set platform (Desktop, Mobile, HoloLens,X-Box and IoT) specific properties.

-   New SystemLog viewer for all nodes in a mesh in "NMI Admin" section (administrator only)

-   New option to disable plugins from loading

-   New TXT=NMI\_ALERT:\<timeout\> message -- displays a bigger toast to announce new devices

-   New SetUXProperty for Table Fields. New Syntax adds a ThingID as the 4th parameter to precisely point at a Row/Field in a table

-   Enhanced Security around JavaScript

## Version 4.001 -- January 2017

### Major Enhancements:

-   Route Optimization by reversing ORG to GRO as the route for optimized travel time

-   Properties of Properties: Properties can be annotated with properties for hierarchically description of Tags. New Syntax for sub properties is "\[Level1Property\].\[Level2Property\]...". The NMI allows to specify these hierarchies in the "OnUpdateName".

-   Globalization in 5 languages (English -- the default, German, Spanish, French, Italian). Language can be selected via the LCID in TheSessionState and the NMI User Manager settings

## Version 3.220 -- November 10th, 2016

**[NMI Improvements]{.underline}**

> Version 4 preview: New TileFactorX/Y -- allows to specify a divisor that will be used to calculate the final grid size of a control. i.e: TileFactorY=2 means that TileHeight=1 is resulting in TileHeight=1/2 = 0.5. This allows for smaller Controls. Attention: in V3.220 only TileGroup, CollapsibleGroup, TileButton, SmartLabel and StatusLight are supported
>
> New Support for Image links in FacePlates using the macro "\<%I:propertyName%\>"
>
> New Support for DeepLinks in the cloud using /nmiportal?CDEDL=\<PluginGuid\>;\<ScreenGuid\>.

## Version 3.219 -October 16th, 2016

-   SetVersion in InitEngineAssets is no longer needed if the "AssemblyFileVersion" in the AssemblyInfo.cs is set correctly.

-   New eThingEvent: OnPropertyChangedByUX: Fires only when the property was changed via the NMI not any backend acitivities.

-   AddSmartControl has an event sink for a function that was called OnPropertyChange of the property declared in "OnUpdateName". Starting 3.219 this event is using the new "OnPropertyChangeByUX"

## Version 3.218 -- October 10th, 2016

-   Internal improvements

-   New NMI "HidePins" property on Dashboards and Forms

## Version 3.215 -- September 1st ,2016

NMI Internal improvements

-   Improved Global Thing management. Call TheThingRegistry.UnregisterThingGlobally() to unregister a Thing from Global Events

-   New MyServiceHostInfo.Coufs -- Create User On First Start to autogenerate Users for a Relay Node. This only works on blank and brand new nodes

## Version 3.213 -- August 22nd, 2016

-   NMI Addition - all controls now supporting "OnKeyDown" and "OnKeyUp" to hook keyboard input.

-   All Resources on Mono (Linux) in the file system have to be lower case. /ClientBin will be renamed to /clientbin. If you use File.IO API Calls please use "TheCommonUtils.cdeFixupFilename()" to create the correct path under the /clientbin folder (correcting the paths for AWS, Azure, Windows and Mono).

## Version 3.208 -- July 1st, 2016

### BREAKING CHANGES:

-   Plug-ins must be signed, or signature validation must be turned off in app.config (dev only, not supported in production "DontVerifyTrust=true"). In order to increase code-signing verification "VerifyTrustPath=true" can be set to validate the complete code-signing certificate path. This REQUIRES an active internet connection.

-   On Windows 7 signed Services can delay the startup beyond the point of proper starting. We recommend that you put the following lines in your App.config:

    -   \<runtime\>

    -   \<generatePublisherEvidence enabled=\"false\" /\>

    -   \</runtime\>

-   TheServiceHostInfo.MyServiceURL renamed to TheServiceHostInfo.LocalServiceRoute

    Changes to ICDEThing Interface:

    -   Init(): must now indicate completion (return value true or FireEvent(eThingEvents.Initialized, true))

    -   CreateUX(): must now indicate completion (return value true)

    -   Delete(): new method called when thing is unregistered permanently. Disconnect, release or delete any resource including any other thing instances created and owner by this thing

    -   HandleMessage(): now has an additional sender parameter, to be in line with .Net event handler patterns

<!-- -->

-   Plug-in Version must be 3.200 or higher

-   TheThingRegistry.RegisterThing now returns null in more cases than before:

    -   Things without an activated license or entitlement will not be registered.

    -   If a BaseEngine was not specified on a TheThing, it will register but cannot be licensed.

-   NMI Changes

    -   Form Views now encore Labels in front of controls. These labels can be turned off using the Property "NoTE=true".

    -   FldWidth is now the TileWidth for fields in Tables. This was used in 3.1 as well but not consequently enforces as in 3.2

## Version 3.200 -- June 5th, 2016

### BREAKING CHANGES:

-   MyBaseEngine does not longer Support "GetBaseThing().GetBaseThing()" to get to its ICDEInterface. Now just use "GetBaseInterface()"

-   Rename of "ctrlReveilButton" to "ctrlRevealButton"

-   New NMI Properties on ctrlTileGroup and ctrlCollapsibleGroup: IsHScrollable and IsVScrollable

-   Allows to make a TileGroup or CollapsibleGroup horizontally or vertically scrollable

## Version 3.125 -- May 23rd, 2016

-   Minor bug fixes for faster Shutdown and NMI Portal Setting

## Version 3.124 -- April 10th, 2016

-   New APIs for Probing of Engine/Service status:

    -   **TheThingRegistry.IsEngineStarted(\<EngineName\>,\<AllowRemote) --** returns true if the Engine has been started by TheEngineHost. It has NOT Initialized but it is considered a live Engine on the current node

    -   **TheThingRegistry.IsEngineInitialized (\<EngineName\>,\<AllowRemote)** -- returns true if the Engine has been Initialized (SetInitialized() was called as part of "ProcessInitialized()". At this point the Engine/Service is fully operational.

## Version 3.123 -- April 5th, 2016

-   New "Updater" functions on cdeP

    -   **SetUpdater(\<guid\>)** - sets a Guid of TheThing that has updated the property

    -   **GetUpdater()** return the Guid -- TheThing can test in an OnPropertyChange event if it was the setter and if the setter and the OnPropertyChange receiver are the same, NOT act on the change event

    -   **ResetUpdater()** -- Resets the Guid for the next setter

-   For security reasons unscoped Relays are no longer allowed to connect to CloudServiceRoutes

-   New "Tag" Property on NMI Controls allowing to set a cookie with any NMI Control

-   Fix for dots (.) in Property Names

## Version 3.122 -- March 29th, 2016

-   Improvement for ISB Path Management to allow legal and wanted MITM Gateways

-   Improved cdeStatus.aspx page

-   Improved Subscription Management in Cloud

## Version 3.121 -- March 23rd, 2016

-   Improved PubSub -- DirectPub to Node not going over complete mesh once delivered

-   CDE\_SYSTEMWIDE now bound to SCOPEID in Mesh

-   cdeNMI improvements for Float and HorizontalAlignment

-   No API Changes

## Version 3.120 -- March 14th ,2016

-   BREAKING CHANGE:

-   The engine now sends different SetProperty calls to clients for SetUXProperty (now sends "SETNP") and Set(Thing)Property (continues to send "SETP").

-   New and much improved NMI Controls management increasing the UX performance in browsers

-   New WebSocketSharp Stack for better Firefox compatibility on Windows 7 and XP

-   Lots of reference documentation updates on <http://www.C-Labs.com/docu>

## Version 3.118 -- February 29, 2016

-   New TheThing Throttling API to reduce excessive property changes being sent to clients

-   Many performance enhancements

-   More reference documentation

## Version 3.117 -- February 21, 2016

-   Breaking API Changes:

> TheBaseAssets.MyServiceHostInfo.IgnoreAdminCheck = true, (was "IgnoreAdmin")

-   New UseRandomDeviceID behavior:

> TheBaseAssets.MyServiceHostInfo.UseRandomDeviceID
>
> Can no longer be set by the Host or plugins and has to be set at Startup by either adding to the App.Config:
>
> Or adding the setting to the ArgList (command line parameter):
>
> ArgList.Add(\"UseRandomDeviceID\", \"true\");

## Version 3.115 -- February 1st, 2016

-   Life FacePlates on TheDashPanelInfo

-   Improved startup sequence of relay

-   Stability improvements

-   New APIs to enable the publishing events on properties: TheThing.SetPropertyEvents(pName, true\|false);

## Version 3.114 -- January 21th, 2016

-   New FacePlate Support for TheDashPanelInfo and TileButtons.

-   New ctrlFacePlate for complex HMTL5 based face plates

-   Support for Windows UAP applications

-   New templates for "cdeServiceWithThings"

## Version 3.113 -- January 6 th, 2016

-   Improved NMI compatibility between JavaScript and C\# plugin

## Version 3.111 -- December 5th, 2015

-   New improved APIs for NMI

-   Synchronized with Factory-Relay V1.111

-   Bug Fixes in cdeNMI JavaScript engine

-   New ThePropertyBag class for dynamic control properties

-   New nmiCtrlxxx classes for Control Property Bags

-   Removed references to C-Labs LLC (now C-Labs Corporation)

## Version 3.084 -- June 7th, 2015

-   Improvements for Connection with Cloud

-   Security enhancements

-   UPnP Enhancements

## Version 3.083 -- June 2nd, 2015

-   New "SecureProperties": Properties and NMI Controls marked as secure are stored encrypted automatically. To Declare a secure property use:

> \<TheThing\>.DeclareSecureProperty("\<propertyName\>");

Any NMI control that is set using the control type "ctrlPassword" will automatically create a secure property for the OnUpdateValue




­­­
