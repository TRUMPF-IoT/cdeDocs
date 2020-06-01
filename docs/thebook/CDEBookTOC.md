
# Developing Solutions with the C-DEngine

## Welcome

## Introduction to C-DEngine

## Pre-requirements and terminologies

### Requirements

### Terminology
* Relay, Node, Host
* Connectivity
* Transport
* Service, Engine, Plugin

## C-DEngine Solution

### The Unique Features of C-DEngine Solutions

#### Universal, secure and IT friendly connectivity and transport
#### Automatic and Integrated User Interface - the NMI
#### Rich extensibility options 

### The Three Main Solution Components explained

#### 1: Host Application
#### 2: C-DEngine
#### 3: Plugins

##  C-DEngine Connectivity

### Outbound vs VPN
### Incoming data vs inbound connections
### The C-DEngine Transport Protocol (CDE-T)
#### Comparison to other protocols
### TSM - The System Message
### How do nodes communicate
### Deep inside TheQueuedSender
### How does PubSub work with the C-DEngine
### NodeTypes explained
### SenderType vs NodeType
### How to use the C-DEngine as a WebServer
### The Cloud-Relay explained
### REST vs WebSockets
### ContentService explained
### Mini-Relays explained
### System Defined Messages

## The C-DEngine Security

### Application ID explained
### Scope ID explained
### UserManager vs ScopeManager
### How does encryption work
### How to secure an on-premises relay
### Using RandomDeviceID

##  C-DEngine Mesh

### AdHoc Scopes
### PubSub vs Client/Server

## C-DEngine Storage

### StorageMirror explained
### StorageMirrorCache explained
### PropertyBags explained
### TheThingStore explained
### CDE Historian explained
### Develop custom and external storage plugins
#### MSSQL Plugin
#### Mongo Plugin
#### Influx Plugin

## C-DEngine Digital Twins - the ThingService

### The ThingRegistry explained
### TheThing object model
### Lifecycle of things
### ICDEThing vs TheThing
### Local vs Global Properties
### Registering Thing Events
### Common Base Properties of Things
### Thread Safe Property access
### Secure Properties

## C-DEngine Diagnostics

#### cdeStatus.aspx explained
#### Logging Options
#### KPIs explained

## The NMI - Natural Machine Interface

### Backend in C#
#### Adding NMI controls to Plugins
#### Binding NMI controls to Thing Properties
#### Common NMI Control properties
#### Grouping controls
#### Securing Control Access
#### FirstNode vs CloudNode vs RelayNode access

### Frontend in TypeScript
#### WebPlatforms explained

### NMI Helpers
#### Using the SensorTemplate

### NMI Deep Dive
#### NMI Object Hierarchy
#### NMI Tables, Forms and Charts
#### NMI Security explained
#### Communication patterns of the NMI
#### The Meta language of the NMI (JSON)
#### Skinning the NMI
#### Globalization of the NMI
#### Tuning for HoloLens, Mobile Phones and Tesla Screens

### How to Extend the NMI
#### Using the NMI NPM package
#### TheNMIBaseControl
#### Custom Controls - UserControls

## Deployment of C-DEngine solutions
### Operator vs Supervisor vs Administrator
### User Levels explained
### Backup and Restore
### installing new plugins
### C-DEngine Installation file CDEX explained
### Upgrading a relay
### Configuration Settings

## C-DEngine Hosts

### CDE in IIS
### CDE in ASP.NET
### CDE in Console App
### CDE in UWP
### CDE in Docker
### CDE in Windows Service
### CDE in Browser
### CDE in NodeJS
### CDE in Xamarin

## C-DEngine Plugin Development
### Digital Twin Plugins (Drivers)
### Pre-Processor Plugins (Local Services)
### Connector Plugins (Cloud Connect Services)
### Diagnostics Plugins 
### NMI Extension Plugins
### Packaging Plugins for installation
### Deploying plugins

### Deep Dives
#### Running plugins in Isolation mode
#### Managing plugins

## A sample Solution

### Creating a host
### Creating a Plugin
### Connecting to the cloud relay
### Adding another node to the mesh
### Creating an installer
### Customizing the NMI with Skins
### Securing the Host (SSL/TLS configuration)

## 


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTYwMTkwNzY0M119
-->