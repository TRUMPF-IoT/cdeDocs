# Sensor Pipeline Model

## Goals

The primary goal of the Sensor Pipeline Model is to make it easier to configure and manage applications built on top of the C-DEngine Plug-in and Thing Models, that consist of multiple, coorperating things and plug-ins.

Specific scenarios that the sensor model aims to address:

1. Creation of sensor pipeline configurations on one system ("Engineering Station") and installation of the configuration on a different system.
2. Tracking of configuration changes to sensor pipeline over time
3. Rollback to previous configurations
4. Cloning ("generalization") and customization ("specialization") of a sensor pipeline to create multiple parallel instances of that pipeline (on standalone system, on the same system or in the same mesh), i.e. one pipeline instance for each machine.
5. Automatic discovery of dependencies between things, and automatic creation of a sensor pipeline configuration for those things.
6. Enable generic (or greatly simplified) configuration tooling by describing configuration, sensor values/types and dependencies, and making them manageable through common APIs.

### Non-Goals

The Sensor Pipeline Model does not:

- Define new mechanism with which things and plug-ins communicate with each other. The Sensor Pipeline Model merely captures the fact that things interact with each other, and makes those interactions manageable in a consistent way.

## Sensor Pipeline Examples

### ModBus Client/OPC UA Client -> Machine Thing -> IoTHub Sender/local Grafana dashboard

## Pipeline Model Building Blocks: Configuration, Sensor Containers, Sensor Providers, Sensor Processors

### Configuration Model

The Configuration Model lets a plug-in developer declare that certain properties of a things represent configuration values. In addition, developers can specify additional information like data type, default value.

With the declarations of the Configuration Model, the TheThing.GetThingConfigurationAsync API can extract only the configuration properties of a thing, which  can then be passed to the TheThing.CreateThingFromConfigurationAsync API to reconstitute the thing or re-apply the configuration values to an existing thing.

#### Generalization and Specialization

 The information about a configuration property includes an indicator if the value needs to be removed when creating a copy of the thing (i.e. for a different machine or environment).

    Example: The OPC UA plug-in has an Address property, that indicates the IP address/DNS name of the OPC UA Server it should connect to. It also  has optional username and password properties for secure OPC UA Servers. In addition there are many "knobs" that control session timeouts, keep alive/reconnect intervals etc.. Then there are properties that merely provide information about the current status of the connections (KPIs, connected/connecting/disconnected).

With the Configuration Model, the Address property is marked as "Required" as the plug-in will simply not function (connect to anything) with a value. The Address property is also marked as needing to be generalized when creating a copy, because another machine will have a different IP address/DNS name. The Username/Password properties are also marked as generalized, as they also tend to be different between machines.

#### Configuration Templates

Configuration Templates can be used with certain plug-ins to automatically include all things of a certain characteristic (currently EngineName/DeviceType). It is up to the plug-ins to properly resolve the "wild-card" thing references, but there are APIs that make this easier (TheThingReference, TheThingFinder).
Configuration Templates are currently not formalized. In future releases, additional capabilities and meta-data may be added to streamline  generating and applying Configuration Templates. 

### Sensor Containers

Sensor Containers are things that provide information about which of their properties represent sensor values (as opposed to configuration values, KPIs or state).

The Sensor Model lets developers declare which properties are sensor values, and provide additional meta data about the sensor values like data type, units, or the origin of the sensor value.

Configuration tooling can then use the sensor meta data to offer improved experiences by showing only things/properties that provide sensor data.

### Sensor Consumers

Sensor Consumers are things that obtain sensor values from other things (and typically transfer them to other systems). An example is the AXOOM IoTHub Sender that can be configured to get data from arbitrary things and send them to the AXOOM IoTHub using configurable parameters like sample rate, buffer sizes etc.

The Sensor Consumer Model lets developers provide information about which things/properties their plug-in is consuming, as well as mechanisms (TSM messages), through which additional things can be added (or removed/modified) to the consumer ("Thing Subscriptions").

Thing Subscriptions become part of the thing's configuration when exported using the TheTHing.GetThingConfigurationAsync, and thing references are resolved/fixed up when creating a copy of a thing as part of a Sensor Pipeline.

Configuration tooling can use the Thing Subscription information to represent dependencies between things, as well as add, remove or modify thing subscriptions in a consistent way across plug-ins.

### Sensor Providers

Sensor Providers are things that offer sensor values, typically from an external system (i.e. the OPC UA CLient plug-in that obtains sensor values from an OPC UA Server).

The Sensor Provider Model lets developers provide information about which sensor values the provider thing is producing ("Sensor Subscriptions"), and into which things and properties those sensor values are being written. It also lets developers provide mechanisms (TSM messages) through which Sensor Subscriptions can be added, removed or modified. It also provides mechanisms to discover what additional Sensor Values are available from the provider (i.e OPC UA browse).

Configuration tooling can use the Sensor Subscription information to represent dependencies between things, as well as add, remove or modify Sensor Subscriptions in a consistent way across plug-ins.

### Sensor Processors

Sensor Processors are things that obtain sensor values from other things and generate other sensor values from those values and typically place those generated sensor values back into the originating thing. They may also place them into a third thing.

Sensor Processors behave like sensor consumers (for the input sensor values) and sensor providers (for the generated sensor values). Currently no additional specific behaviors are defined for Sensor Processors.

## Sensor Pipeline APIs

Note: the purpose of this section is to provide an overview with a focus on the concepts. For details of the classes and methods please refer to the reference documentation.

### Pipeline Import/Export APIs

All the mechanisms described below combine to enable Pipeline export and import:

#### TheThing.GetThingPipelineConfigurationAsync

This API determines all the incoming and outgoing dependencies of a thing (thing or sensor subscriptions) and returns the configuration for all the things in the dependency graph.

#### TheThing.CreateThingPipelineFromConfigurationAsync

This API re-creates or updates a Sensor Pipeline with the configuration supplied. The configuration can be generated with the GetThingPipelineConfigurationAsync API, or authored through other tooling or even by hand.

#### class TheThingPipeline

A thing pipeline is represented by related collections:

- Thing Configurations: the configuration of each of the things in the pipeline
- Specialization Parameters: a name-value-pair collection for each Thing Configuration, that provides any configuration values that are required to create an independent copy of the pipeline.

### Configuration APIs

#### Classes

At the core of the configuration API are the following classes:

##### Exported configuration: TheThingConfiguration

This class captures the configuration of a thing. It consists primarily of:

- The identity of the thing that the configuration belongs to.
- A collection of all the configuration properties of the thing.
  - In the case of a generalized thing (a thing that is to be copied), a list of properties that need to be specified.
  - Advanced: Custom Configuration - some things may have custom state that is not represented as thing properties (i.e. storage mirrors with complex data). Plug-in developers can provide that information in the form of a name-value pair collection that is not further defined or interpreted by the C-DEngine or the configuration management APIs.
- The Sensor Subscriptions that feed sensor data into the thing.
- The Thing Subscriptions that the thing consumes.

##### Configuration property meta data: TheConfigurationProperty

This class captures the meta data about a property that represents a configuration value. It consists primarily of:

- Name of the property.
- Data Type of the property
- Units and Range of the values
- Default Value: the value that the plug-in will apply if no actual value is specified.
- Current Value
- Generalize: indication if the property value should be removed when exporting the configuration for the purpose of copying the thing (as opposed to moving it or backing it up).
- Secure: indicate if the property needs to be encypted

#### Working with configurable things

The following APIs are typically used by configuration tooling.

##### TheThing.GetThingConfigurationAsync()

Returns the configuration of the thing in the form of a TheThingConfiguration instance. A parameter indicates if the configuration should be generalized (for copy) or not (for backup/restore or moving of the thing instance).

##### TheThing.CreateThingFromConfigurationAsync()

Creates an instance of the thing and populates it with the configuration from a TheThingConfiguration instance. It tries to find an existing thing that matches the Thing Identity and re-applies the configuration if it finds one.

##### TheThing.ApplyThingConfigurationAsync()

Applies the configuration to an existing thing.

##### cdeP.GetConfigMeta() / SetConfigMeta(): Obtaining or modifying meta data about a configuration property

Returns or applies a TheConfigurationProperty instance.

##### cdeP.IsConfig: Check if a property is a configuration property

#### Making plug-ins manageable

The following APIs are used by plug-in developers to make their plug-ins manageable with the configuration model APIs.

##### Marking a plug-in as supporting the Configuration Model: `eThingCaps.ConfigManagement`

Developers must mark Plug-ins conforming to the configuration model with the `eThingCaps.ConfigManagement` capability. Both the engine and the things needs to be marked with the capability as follows:

```C#
// Engine that contains at least one thing with the capability
MyBaseEngine.AddCapability(eThingCaps.ConfigManagement);
```

```C#
// Thing that offers the capability
MyBaseThing.AddCapability(eThingCaps.ConfigManagement);
```

The engine instance itself may or may not support configuration management, and the developer can indicate this by marking of not marking the engine's base thing, which is separate from the capability on the engine itself.

##### TheThing.DeclareConfigProperty(): Declaring a property as a configuration value

Plug-in developer typically use this method in the TheThing.Init() implementation to declare, create and initialize properties that contain configuration values.

##### MsgApplyConfig / cdeRequiresCustomConfig: Applying configuration

Some plug-ins may not be able to pick up configuration changes "on the fly", i.e. the OPC UA plug-in currently needs to disconnect/reconnect in order to let some of the configuration values take effect and hence doesn't pick those up until the user (or a calling plug-in) explicitly reconnects. 
These plug-ins must handle a TSM with a TXT of `MsgApplyConfig` and perform the actions necessary to apply any configuration changes that were previously written to the configuration properties.
In order to opt-in into this mechanism, the plug-in must create a `cdeRequiresCustomConfig` property and set it to `true`.

### Sensor Container APIs

#### Sensor Classes

##### TheSensorMeta

This class captures meta data about a property that carries sensor data. It consists of the following:

- Units / Range: information about the units and expected ranges of the sensor
- Source Type: data type of an external system (i.e. OPC UA data types)

#### Working with sensor properties

##### cdeP.IsSensor

Checks if a property is marked as a sensor property.

##### TheThing.GetSensorProperties()

Gets all properties that are marked as sensor properties in a thing.

##### cdeP.GetSensorMeta()

Retrieves the TheSensorMeta for a property.

#### Implementing sensor properties in a plug-in

##### TheThing.DeclareSensorProperty()

Often used in the TheThing.Init() function of a plug-in or whenever a new sensor property is added at runtime (i.e. after a browse & subscribe operation in the OPC UA plug-in).

##### cdeP.SetSensorMeta()

SetSensorMeta can be used to modify the sensor meta data, but in most cases this should be done only by the owning plug-in itself when a sensor's meta data changes or is established for the first time, not by a separate consumer plug-in/app.

### Sensor Consumer APIs

These APIs enable configuration tooling to discover and manipulate the things that are consumed by a Sensor Consumer plug-in/thing.

#### Sensor Consumer Classes

##### TheThingSubscription

This class captures which thing and properties a Sensor Consumer is processing, as well as optional filters on the property stream update:

- SubscriptionId: unique identifier for the subscription, allocated by the consumer thing/plug-in. Used for update/delete operations, to allow for multiple different subscriptions to the same thing.
- Thing Reference: reference to the thing(s) that are being consumer. Some plug-ins support "wild card" referneces where only Engine Name/Device Type can be specified, with the plug-in automatically consuming sensor data from all matching thing instances.
- Properties Included: list of properties that are consumed. Some plug-ins may only support a single property. If the list is empty/null, all properties of the thing are consumed.
- Properties Excluded: list of properties to exclude (if using an empty properties included list)
- Sampling Window, Cooldown Period etc.: Parameters commonly used by plug-ins that consume thing/property update streams through the Thing History feature (i.e. TheThing.GetHistoryAsync() API).

#### Working with Sensor Consumers

##### Discover all Sensor Consumers

Sensor consumers must be marked with the eThingCaps.SensorConsumer capability and can be discovered using the TheThingRegistry.GetAllDeviceTypesByCap API:

```C#
// All DeviceTypes that are sensor consumers
var deviceTypes = TheThingRegistry.GetAllDeviceTypesByCap(nsCDEngine.ViewModels.eThingCaps.SensorConsumer, true);
// All currently existing instances of these device types
var thingConsumers = deviceTypes.SelectMany(dt => TheThingRegistry.GetThingsOfEngine(dt.EngineName).Where(tThing => tThing.DeviceType == dt.DeviceType));
```

#### TheThing.GetThingSubscriptionsAsync()

Gets all thing subscriptions of a Sensor Consumer.

#### TheThing.UnsubscribeFromThingsAsync()

Removes thing subscriptions from a sensor consumer, passing in the subscription id.

#### TheThing.SubscribeToThingsAsync()

Adds or updates the thing subscriptions of a consumer. Can optionally remove any subscriptions that are not in the list, so that the final list of subscriptions of the consumer is exactly the set of subscriptions that were passed to the API call.

#### Implementing a Sensor Consumer plug-in

##### eThingCaps.SensorConsumer capability

Plug-ins/things that consume sensor data, must be marked with the eThingCaps.SensorConsumer capability (see [Capability Discovery](Capability-Discovery)).

##### Messages: MsgGetThingSubscriptions, MsgSubscribeToThings, MsgUnsubscribeFromThings

Any plug-in/thing marked with the eThingCaps.SensorConsumer capability must handle these three TSM messages in their ICDEThing.HandleMessage implementation:

- MsgGetThingSubscriptions / MsgGetThingSubscriptionsResponse
- MsgSubscribeToThings / MsgSubscribeToThingsResponse
- MsgUnsubscribeFromThings / MsgUnsubscribeFromThingsSensors

### Sensor Provider APIs

These APIs enable configuration tooling to discover and manipulate the things that provider or generate sensor data.

#### Sensor Provider  Classes

##### TheSensorSubscription

This class captures a single sensor value that is provided by a sensor provider thing. It consists of the following:

- Sensor Id: the identifier of the sensor in the provider's system. Example: for the OPC UA Client the sensor Id is the OPC UA NodeId.
- Target Property: the name of the property that contains the sensor's value.
- Target Thing: a  reference to the thing that contains the Target Property. Some sensor providers will only allow sensor subscription into their own thing, not into a separate thing.
- Subscription Id: A Guid identifying the sensor subscription for management purposes (subscription add/remove/update). The guid must remain stable for the lifetime of the sensor, including across system reboots.
- Sample Rate: limits the frequence of sensor updates that are to be generated. Not all plug-ins will honor the sample rate.
- Custom Subscription Info: additional properties can be defined by each sensor provider. Example: the OPC UA Client plug-in supports ChangeTrigger, DeadbandValue and QueueSize.

#### Working with Sensor Providers

##### Discover all Sensor Providers

Sensor providers must be marked with the eThingCaps.SensorProvider capability and can be discovered using the TheThingRegistry.GetAllDeviceTypesByCap() API:

```C#
// All DeviceTypes that are sensor providers
var deviceTypes = TheThingRegistry.GetAllDeviceTypesByCap(nsCDEngine.ViewModels.eThingCaps.SensorProvider, true);
// All currently existing instances of these device types
var thingConsumers = deviceTypes.SelectMany(dt => TheThingRegistry.GetThingsOfEngine(dt.EngineName).Where(tThing => tThing.DeviceType == dt.DeviceType));
```

See [Capability Discovery](Capability-Discovery) for more details.

#### TheThing.GetSensorSubscriptionsAsync()

Gets all sensor values currently provided by a Sensor Provider.

#### TheThing.UnsubscribeSensorsAsync()

Removes a sensor value subscription from a Sensor Provider (passing in the subscription id).

#### TheThing.SubscribeSensorsAsync()

Adds or updates the sensor value subscriptions of a Sensor Provider. Can optionally remove any sensor value subscriptions that are not in the list, so that the final list of subscriptions of the Sensor Provider is exactly the set of subscriptions that were passed to the API call.

#### TheThing.ProviderBrowseSensorsAsync()

Discovers the available sensor values that a Sensor Provider can provide. The resulting information can be used to create Sensor Subscriptions. Not all Sensor Providers offer browse capability, i.e. the ModBus plug-in (currently) only returns currently defined subscriptions as the ModBus protocol does not offer discovery capability.

#### Implementing a Sensor Provider Plug-in

##### eThingCaps.SensorProvider capability

Plug-ins/things that consume sensor data, must be marked with the eThingCaps.SensorProvider capability (see [Capability Discovery](Capability-Discovery)).

##### Messages: MsgGetSensorSubscriptions, MsgSubscribeSensors, MsgUnsubscribeSensors, MsgBrowseSensors

Any plug-in/thing marked with the eThingCaps.SensorProvider  capability must handle these four TSM messages in their ICDEThing.HandleMessage implementation:

- MsgGetSensorSubscriptions / MsgGetSensorSubscriptionsResponse
- MsgSubscribeSensors / MsgSubscribeSensorsResponse
- MsgUnsubscribeSensors / MsgUnsubscribeSensorsResponse
- MsgBrowseSensors / MsgBrowseSensorsResponse

## [Capability Discovery](Capability-Discovery)

### TheThingRegistry.GetAllDeviceTypesByCap

This API returns DeviceTypes (EngineName and DeviceType) that are marked with a given capability.
```C#
// All DeviceTypes that are sensor providers
var deviceTypes = TheThingRegistry.GetAllDeviceTypesByCap(eThingCaps.SensorProvider, true);
```

Caller will typically use this to either find all existing instances:

```C#
// All DeviceTypes that are sensor providers
var deviceTypes = TheThingRegistry.GetAllDeviceTypesByCap(eThingCaps.SensorProvider, true);
```

Or to offer a choice to a user as to which instance to create:

```C#
var tThing = await TheThingRegistry.CreateOwnedThingAsync(new TheThingRegistry.MsgCreateThingRequestV1
{
    OwnerAddress = owner,
    CreateIfNotExist = true,
    EngineName = deviceType.EngineName,
    DeviceType = deviceType.DeviceType,
});
```

### IBaseEngine.SetDeviceTypes(): Declaring which device types a plug-in supports and what their capabilities are

In order to enable discovery of plug-ins with a certain capability, plug-ins should declare which device types they support in their InitEngineAssets() implementation. For each device type, the developer can specify the supported capabilities and a description.

Example:

```C#
MyBaseEngine.SetDeviceTypes(new List<TheDeviceTypeInfo>
{
    new TheDeviceTypeInfo
    {
        Capabilities = new eThingCaps[] { eThingCaps.SensorConsumer, eThingCaps.ConfigManagement },
        DeviceType = AxoomDeviceTypes.AxoomIoTSender,
        Description ="AXOOM IoTHub Sender"
    },
});
```
