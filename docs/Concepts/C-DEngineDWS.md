# DWS --Distributed Web Service

Welcome!
========

The Distributed Web Service (DWS) of the C-DEngine lets you configure resource on one node and consume than on any other node or browser connected to any node of a mesh.

### Resources

+ [What is a "Mesh"?](C-DEngineMeshExplained)


Table of Contents
========

+ [Welcome!](#welcome)
+ [Overview](#overview)
+ [Requirements](#requirements)
+ [Installation](#installation)
+ [Configuration](#configuration)
+ [Basic Operation](#basic-operation)
+ [Heading](#_Toc421917383)
+ [Heading](#_Toc421917384)
+ [Advanced Topics](#_Toc421917385)

Overview
========

This document describes the usage of the Distributed Web Service (DWS)
of the C-DEngine. It is meant for Web Designers, Web Developers and IT
personal in charge of setting up distributed IoT Web Scenarios in
Industrial Environments.

Requirements
============

The DWS is part of the C-DEngine but in order for the DWS to be active,
the C-DEngine must be hosted in a "Relay". An example for this Relay is
the "Factory-Relay" or similar product. With the C-DEngine SDK,
developers can easily build their own Relays and DWS environments.

Installation
============

There is no special installation necessary to activate the DWS
functionality of a relay. Please follow the instructions to go through the ["Hello World - Getting Started" of the C-DEngine](https://github.com/TRUMPF-IoT/C-DEDocs/blob/master/docs/HelloWorld.md).

Configuration
=============

Each Relay in a C-DEngine mesh is a DWS.

In order for all DWS to work in conjunction, all participating relays
have to use the same Scope ID.

For production Relays, the Scope ID can be set in the NMI, in the SDK Sample you can hardcode the ScopeID in your startup file.

Basic Operation
===============

For security reasons, the DWS is meant for static pages only. i.e.
Manuals, Help Text, Device Information etc.

If dynamic content is required, a plug-in for the Relay is required that
has to provide the dynamic content. 

Basic functionality of the DWS
------------------------------

Once a Relay starts for the first time, the relay will create a
"ClientBin" sub-folder next to the Relay executable.

The DWS will always look for content in this folder and its sub-folders
to serve out to connected browsers.

Hosting a HTML Page in the DWS
------------------------------

1.  Create a small HTML page with the following content and name it
    "relay.html".

```html
<html>
 <body>
  <h1>This is My Relay Server</h1>
 </body>
</html>
```

2.  Now copy this page in the ClientBin folder of your Relay

3.  Next open a browser of your choice and point it at
    "\<yourRelayUrl\>/relay.html" (i.e.
    <http://localhost:8706/relay.html>)

4.  You will see the page immediately showing in your browser.

Linking other content to the page
---------------------------------

As HTML content is rendered and interpreted on the browser, all standard
HTML tags are supported including \<a\> (anchor) and \<img\>.

Since the DWS is a "Distributed Web Service" you can link to content on
other Relays (within the same scope) as if all the content is located on
the same server.

1.  Create a folder "Images" under the "ClientBin" folder

2.  Find a nice royalty and license free JPEG image on the web and copy
    it into the "ClientBin/Images" folder and rename it to "myimg.jpg"

3.  Extend your HMTL page with the following Image Tag

```html
<html>
 <body>
  <h1>This is My Relay Server</h1>
  <img src="/images/myimg.jpg"/>
 </body>
</html>
```

Now load the page in your browser and you will see the page with the
image as expected.

Distributed Web Browsing
------------------------

If you have a mesh with multiple Relays you can now distribute
the HTML resources between all the relays.

Just move the "/Images" folder under ClientBin of another relay within
the same mesh

Now open your browser again and you will still see the same page with
the images.

If you do the same with an .HTML page, you might see a "Waiting for
page..." screen with a counter counting backwards from 10 to zero. The
DWS is showing this page to tell the user that it is locating the
requested page inside the Mesh. This can take a couple seconds depending
on the size of the mesh and the configuration of the mesh (HTTP vs
WebSockets).

Distributed Cloud Web Browsing
------------------------------

If your relay is connected to the cloud, you can connect to your site,
once you have established at least one secure connection to your relay.
Without a previously established connection, the browser and the cloud
will not know which relay it has to connect to.

Make sure your Relay node is connected to the cloud.

1.  Go to the Relay plug-in screen and click on the "Cloud
    Setup" button. If it shows "Public Cloud" you are connected to the
    Relay cloud. (In the SDK Getting Started demo you set the "ServiceRoute" to the Cloud)

2.  Open a browser and point at
    [cloud.C-Labs.com/NMI](https://cloud.C-Labs.com/NMI)

3.  Enter your scopeID for your Relay.

4.  Once you see the main portal of the Relay go to another tab
    in the same browser.

It is important that you use the same browser and not open a new browser
as the cookie of the C-DEngine is only shared between tabs of the same
instance of a browser. As soon as you close the browser or use another
one, the session is no longer valid and the cookie is automatically
deleted.

5.  []{#_Toc421917384 .anchor}Now enter
    [https://cloud.C-Labs.com/relay.html](https://cloud.C-Labs.com/relay.html)
    in the URL of the new tab

You will immediately see the "Waiting for Page..." screen.

Then the page refreshes and you will see the page with the "This is My
Relay Server" and the image on it.

Advanced Topics
===============

Caching
-------

Web Server and Browser have a very complicated relationship when it
comes to caching. The Server can offer a cache period and the browser
can use it or use its own caching. Meta Tags in the header of the HTTP
Message and meta tags in the header of the HTML page can also influence
the caching behavior.

For security reasons, the C-DEngine enforces its own caching for
content:

-   HTML Pages are cached for 30 seconds in the "first node". (The first
    node is the node the browser is connected to).

-   PDFs, Icons, Fonts, ZIP Files, Style Sheets (CSS) and Images are
    cached until the Node restarts.

-   XML, JSON are not cached at all and will be requested from the mesh
    nodes every time

The cache is Scope aware and therefore Multi-Tenant enabled. This means
that two Relays of two customers can have the same image or HTML
Page name but only see their own resources -- not the resource of any
other customer.

Synchronous vs Asynchronous content
-----------------------------------

The HTTP protocol was invented in the 1970s and has not evolved much
until now. It is still inherently synchronous and not able to do
asynchronous work unless a JavaScript engine such as the C-DEngine's own
"cdeNMI.js JavaScript-Engine" is used.

If a browser requests a HTML page, it sends a request to the server and
blocks the request thread until the page has returned.

The server has to return the page in the response to the incoming
request.

In asynchronous environments, the browser would request a page, and the
server returns the page whenever the server has gathered all the
information. In some cases, this can be many seconds later and not in
the response of the incoming request. The JavaScript C-DEngine is using
the principle to send pages and content asynchronously between the DWS
and the browser. Unfortunately, this works only for data and dynamic
updating content -- not for static content such as image resources and
html pages.

Since the DWS sometimes has to gather synchronous content from multiple
nodes before it sends the content back to the browser, the C-DEngine DWS
is holding back the Response in the server for a certain amount of time
until the content is delivered from other nodes. Once all content for
the request is assembled it sends it back to the browser.

HTML Pages are not held in the server. If a HTML page is not on the
"First Node" the first node DWS is sending the "Waiting for Page..."
page back to the browser. This page contains a "Meta-Refresh" tag that
forces the browser to try again to fetch the page after 10 seconds. In
the meantime, the DWS is gathering the page from the other nodes, caches
it and once the 10 seconds are up -- serves the page back to the
browser.

All this is done automatically by the DWS and developers do not need to
be aware of this.

Because of this HTTP limitation, most HTML content is provided
synchronously. Exceptions are ZIPfiles, XAP (Silverlight Content) and
Fonts. Some browsers do automatically retry these resources if they get
a 408 (Request Timeout) from the server.

Redundant content
-----------------

One of the cool features of the DWS is the possibility to create
redundant content.

Basically all you have to do is to copy the same content on all nodes in
the mesh. The content DWS is trying to locate content for any node in
the mesh -- if a node went down that had content on it, another node
automatically provides the content.

But as always ...every magic comes at a price: If you have different
content in the same file name (i.e. /images/star.jpg is a different star
on another node), you might have unpredictable behavior in the browser.
The content of the node that replies first to a DWS request will be
used!

Content Authoring
=================

The C-DEngine (C-Labs Distributed Engine) allows to transfer, combine
and view content across the C-DEngine Mesh. This can be used for dynamic
web sites as well.

The NMI of the C-DEngine (for example the "My Relay Portal") is an
example of distributed content viewing and a central part of the
Relay.

Developers can use the C-DEngine Platform to build their own distributed
Web Solution.

[www.c-labs.com](http://www.c-labs.com) is an example of a distributed
web solution. Some of the resources on
[www.c-labs.com](http://www.c-labs.com) are provided from different
nodes in the [www.c-labs.com](http://www.c-labs.com) mesh. For example
the main static pages are on the [www.c-labs.com](http://www.c-labs.com)
cloud webserver while documents, whitepapers and other resources are
stored on a different node in our secure network at C-Labs.

[www.C-Labs.com](http://www.C-Labs.com) is completely served through the
C-DEngine and most of its business logic and content is hosted inside a
C-DEngine Plug-In (C-DMyC-Labs)

It is important to understand that there is a big difference between
traditional web-sites with static and dynamic content and the C-DEngine
NMI (Natural Machine Interface):

Traditional Websites assemble the pages on the server and inject dynamic
content at that time. Once the page is delivered to the browser, the
content is static - not updating on the page without refreshing the
page.

C-DEngine NMI Pages are highly dynamic in the browser and update content
on the fly without refreshing the page manually. In fact, if the page
would be refreshed manually (by clicking the refresh button in the
browser), the NMI would drop the session and the user and require that
the user logs in again. Therefore, in complete opposite to traditional
web sites, the user MUST NOT REFRESH the page unless he wants to restart
the NMI portal.

Dynamic DWS Model
-----------------

The Dynamic DWS model describes the pages with records in relational
tables in a database. By default, these tables are created by the NMI
engine in RAM and filled by each plug-in on the fly when the Relay
starts. These tables are called the "C-DEngine DWS Meta Model".

The C-DEngine DWS Meta Model is using three main tables describing the
page content:

-   ThePageDefinition

-   ThePageContent

-   ThePageBlocks

**ThePageDefinition** defines the entry points and general settings of a
"Page". For example, the path of the URL (i.e. "/Products.aspx") or
Title of the page to be used. It can also contain a pointer to a record
in ThePageContent. If the pointer to ThePageContent is not set but a
Template is defined, the DWS will parse the template and send this to
the browser.

**ThePageContent** contains a mapping of a ContentID to ThePageBlocks
and is used to describe the content of a page.

**ThePageBlocks** contains blocks of information and is the actual html
content. ThePageBlocks can be reused in multiple ThePageContent records
in order to reduce the amount of design required for web sites.
ThePageBlocks can also be called "Dialog" or "FacePlates" and can have
dynamic content referenced with macros starting and ending with a "%"
(i.e. "%SITENAME%" will be replaced with the sitename
"TheBaseAssets.MyServiceHostInfo.SiteName'" at the time the page is
served to the browser)

Good examples for ThePageBlocks are footers, headers, sidebar content
blocks, Advertisement blocks, basically anything that needs to be
designed once and reused on multiple pages.

Templates can also include macros that will be interpreted and replaced
in the DWS before the page is sent to the browser. They can even include
macros pointing at ThePageBlock entries for complete flexibility.

Syntax: %PBLOCK:\<GuidOfThePageBlock\>%

Smart Pages
-----------

Smart Pages are shortcuts to Thing UX pages defined in Plug-Ins

FacePlates 
----------

FacePlates are small DIV HTML snipplets that can be used a controls in
NMI UX pages and as the content on TileButtons. The difference of
FacePlates to ThePageBlocks is the possibility to have live-updating
content in the FacePlate if used in conjunction with a plugins Thing
Reference.

Summary
=======

Congratulations! You have been introduced to the DWS Distributed Web
Services of the C-DEngine, and now you should be confident creating your
own distributed Web Solution.


