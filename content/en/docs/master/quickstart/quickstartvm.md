---
title: Using Quickstart VM
linktitle: Using Quickstart VM
toc: true
type: docs
date: "2020-06-15T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Quickstart
    weight: 10

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---

## Prerequisites

* OS: Windows / Linux / MacOS
* [VirtualBox 6.0+](https://www.virtualbox.org/): Oracle VM VirtualBox, is a free and open-source virtual machine. 
* [Ohara Quickstart VM image](https://github.com/oharastream/ohara-quickstart/releases): 
  An OVA (Open Virtual Appliance) file, a pre-prepared virtual machine image for Ohara quickstart. 
  You can download the image file (.ova) from the [release](https://github.com/oharastream/ohara-quickstart/releases/) page.

  {{< figure src="../img/download_assets.png" title="Assets of a release" lightbox="true" >}}

{{% alert tip %}} 
Please download the VirtualBox from [here](https://www.virtualbox.org/wiki/Downloads), 
and reference [this article](https://www.virtualbox.org/manual/ch02.html) on how to install it on your machine. 
{{% /alert %}}

{{% alert tip %}}
You might have noticed there's another jar file listed in the screenshot: "ohara-it-stream.jar". 
It's a stream jar that could be used later in our tutorial where we walks you through how to use our UI. 
And download it if you would like to follow along with our tutorial later on.
{{% /alert %}}

## Installation

### Import VM and setup network adapter

You can use VirtualBox user interface to import the Ohara Quickstart VM (ova file):
{{< hl >}} **_Main menu_** -> **_File_** -> **_Import Appliance_** {{< /hl >}}

Quickstart VM requires a **Host-only network adapter** to be configured so that you can 
connect from the host machine to guest machine (Quickstart VM).

{{% alert note %}}
Quickstart VM uses network adapter `vboxnet0` with DHCP Server enabled as **default Host-only adapter**, 
if there is already a `vboxnet0` adapter in your VirtualBox, you can just skip this step.
{{% /alert %}}


#### for Mac/Linux

1. Create new network adapter --- Click **Tools** and then click **Create**, 
   ensure that the DHCP Enable option is checked.
  {{< figure src="../img/mac_add_network.jpg" title="macos/linux - Create network adapter" lightbox="true" height="100%" width="100%" >}}

2. Setting network adapter --- Select the imported **ohara-quickstart-x.x.x** VM, 
   click **Setting**, then click **Network**, and click **Adapter2**, 
   select **Host-only Adapter**, and select the newly added network card.
  {{< figure src="../img/mac_setting_network.png" title="macos/linux - Setting VM's network adapter" lightbox="true" height="100%" width="100%" >}}


#### for Windows

1. Create new network adapter --- Click **Global Tools** and then click **Create**, 
   ensure that the DHCP Enable option is checked.
  {{< figure src="../img/win_add_network.png" title="Windows - Create network adapter" lightbox="true" height="100%" width="100%" >}}

2. Setting network adapter --- Select the imported **ohara-quickstart-x.x.x** VM, 
   click **Setting**, click **Network**, click **Adapter2**, 
   select **Host-only Adapter**, and select the newly added network card.
  {{< figure src="../img/win_setting_network.png" title="Windows - Setting VM's network adapter" lightbox="true" height="100%" width="100%" >}}

### Install Ohara

Once the Quickstart VM is imported and the network adapter is configured, you can press
the **Start** button to start Quickstart VM and then use the following username and password to log into the system:

- Username: _ohara_
- Password: _oharastream_

The installation will be starting automatically if this is your first time log in to the system. 
This step will take some time to complete as it needs to download all Ohara docker images.

{{< figure src="../img/vm_ohara_install_1.png" title="VM is pulling down images from Docker Hub" lightbox="true" height="100%" width="100%" >}}

{{< figure src="../img/vm_ohara_install_2.png" title="Finishing the setup" lightbox="true" height="100%" width="100%" >}}

After the installation is completed, you should see something like the following:

```console
> Start ohara-configurator...
74e0a19a063ce665a0bafba215827d6edd47b9433efdf26af9880d0b4f5e3737

> Start ohara-manager...

ecc6e2845f55b52c6a2ec4b2a203d249f117394cbc16c5387aa067ee5d02a096
> Ohara ready on http://192.168.56.102:5050

ohara@ohara-vm:~$
```

As we can see here, the VM's IP address is `192.168.56.102` 
(this address will be varied depending on your VirtualBox network settings).
We can then open the browser and enter this URL in browser's address bar 
`http://192.168.56.102:5050` to open Ohara Manager (Ohara's UI, we will 
introduce it in the following section).


{{% alert tip %}}
  After shutting down your VM, the docker containers will be deleted and restarted on your next login
{{% /alert %}}  

## Terminology

Before jumping into the UI and create our very first workspace and pipeline. 
Let's get to know some of the terms that we will be using throughout this guide. 

* Manager

  Manager is the user interface of Ohara (UI). It provides a friendly user interface allowing user to design their data
  pipeline without even touching a single line of code. 

* Node

  Node is the basic unit of running service. It can be either a physical server or virtual machine.

* Workspace

  Workspace contains multiple OharaStream services including: Zookeepers, Brokers and Workers. 
  And pipelines are services that run in a workspace.

* Pipeline

  Pipeline allows you to define your data stream by utilizing **Connector** to connect to 
  external storage systems, as well as a **Stream** to customize data transformation and stream processing.

* Connector

  Connector connects to external storage systems like Databases, HDFS or FTP. 
  It has two types --- source connector and sink connector.
  Source connector is able to pull data from another system and then push the data to topic. 
  By contrast, Sink connector pulls data from topic and then push the data to another system.

* Stream

  Stream is powered by [Kafka Streams](https://kafka.apache.org/documentation/streams/) which 
  provides users a simple way to write their own stream processing application.

* Topic

  A topic is a place where all the data are written just like a database table where data is stored. It acts like a buffer, the data being pull in from the source connector is stored in the topic and so later can be pulled out again by another component (e.g., sink connectors)

## UI overview

Before we proceed, here is a screenshot of **Ohara Manager** where we show you each component's name, so you are better prepared for the upcoming tutorial. You can always come back to this overview if you are lost or not sure what we're talking about in the tutorial.

{{< figure src="../img/ui-overview.png" title="UI overview" lightbox="true" height="100%" width="100%" >}}

{{% alert note %}}
We do our best to make our docs as clear as we could, if you think there's still room for improvement. 
We would love to hear from you: [https://github.com/oharastream/ohara/issues](https://github.com/oharastream/ohara/issues)
{{% /alert %}}

Now, Ohara Manager is up and running, we can use the UI to create our very first pipeline. 
Here are the steps that we will be going through together:

- Create a workspace
- Create a pipeline
- Add pipeline components, this includes:
  - A FTP source and a sink connector
  - Two topics
  - A stream
  - Create connections between them
- Start the pipeline 

{{% alert note %}}
During the tutorial, we will be using FTP source/sink connectors. And so you will need to prepare your own FTP in order to follow along.
{{% /alert %}}


## Create a workspace

Open Ohara Manager with your browser (http://192.168.56.102:5050) and you should see a dialog 
showing up right in the middle of your screen:

- Click on the **QUICK CREATE** button to open a new dialog
{{< figure src="../img/intro-dialog.png" title="Intro dialog" lightbox="true" height="100%" width="100%" >}}
  
- Using the default name: _workspace1_ and hit the **NEXT** button
{{< figure src="../img/quick-create-workspace-name.png" title="Quick create workspace new name" lightbox="true" height="100%" width="100%" >}}

- Click on the Select nodes and click on the pencil icon to create a new node.

{{< figure src="../img/quick-create-workspace-node.png" title="Quick create workspace new node" lightbox="true" height="100%" width="100%" >}}

{{< figure src="../img/quick-create-workspace-add-node.png" title="Quick create workspace add node icon" lightbox="true" height="100%" width="100%" >}}
  
- The node info that you need to enter are listed below: 
  - Hostname: _192.168.56.102_ (fill your own hostname here)
  - Port: _22_
  - User: _ohara_
  - Password: _oharastream_
  
{{< figure src="../img/quick-create-workspace-add-node.png" title="Quick create workspace add a new node" lightbox="true" height="100%" width="100%" >}}

- The node should be added into the list. Select the node and click on the SAVE button to close the dialog
  
{{< figure src="../img/quick-create-workspace-select-node.png" title="Quick create workspace select node" lightbox="true" height="100%" width="100%" >}}

- Now, click the **NEXT** button to finish selecting nodes

{{< figure src="../img/quick-create-workspace-node-summary.png" title="Quick create workspace node summary" lightbox="true" height="100%" width="100%" >}}

- Click on the **SUBMIT** button to create this workspace.

{{< figure src="../img/quick-create-workspace-summary.png" title="Quick create workspace summary" lightbox="true" height="100%" width="100%" >}}
  
- A new popup window will open where it shows you the creating progress. This usually take a while to finish. Once it's done, All popup windows will be closed. And the UI will automatically redirect you to the newly created workspace: _Workspace1_  
  
{{< figure src="../img/quick-create-workspace-progress.png" title="Quick create workspace creating progress" lightbox="true" height="100%" width="100%" >}}

{{< figure src="../img/quick-create-workspace-done.png" title="Quick create workspace done" lightbox="true" height="100%" width="100%" >}}
  

{{% alert tip %}}
You can create more workspace with the plus icon(:heavy_plus_sign:) in the App bar.
{{% /alert %}}

{{% alert warning %}}
Please keep in mind that this is a quick start VM where we run everything on a single node 
with only 8GB of RAM and 2 CPU cores. We would highly recommend you to add more RAM and CPU 
core to your VM if you plan to create more than one workspace or lots of pipelines, connectors, 
streams, etc. This is due to When Ohara is running without enough resources, it could be very 
unstable and causing unexpected errors.
{{% /alert %}}
  
## Create a pipeline

Create a pipeline is fairly simple:

- On the Navigator, click on the plus icon.
- In the popup window, enter the name: pipeline1 and click the **ADD** button
{{< figure src="../img/add-a-pipeline.png" title="Add a pipeline" lightbox="true" height="100%" width="100%" >}}
- The new pipeline will be added into the workspace and listed in the Navigator. 
  And just like creating workspace, you are also redirected into the pipeline you just created. 
{{< figure src="../img/add-a-pipeline-done.png" title="Add a pipeline done" lightbox="true" height="100%" width="100%" >}}

### Add pipeline components

Since workspace and pipeline are both ready. We can now add new components into the pipeline. 
The pipeline connection we're about to create will look like: 
{{< hl >}} FTP source -> topic -> stream -> topic -> FTP sink {{< /hl >}}

Before we start, please make sure your FTP service is ready and let's get started!

#### Drag and drop new pipeline components

**FTP source:**

- From the Toolbox (Please refer to the overview image for what is Toolbox)
- Click on the title "Source", the panel will be expanded and display all available source connectors
  {{< figure src="../img/ftpsource-add-toolbox.png" title="Add a FTP source from Toolbox" lightbox="true" height="100%" width="100%" >}}

- Drag the "FtpSource" from the list and drop into the Paper (Don't worry about the position. This can be changed by simply dragging the component after they're added into the Paper). A prompt will be asking you about the connector name, let's name it "ftpsource" and click the ADD button.
  {{< figure src="../img/ftpsource-add-name.png" title="Add a FTP source name" lightbox="true" height="100%" width="100%" >}}

- The FTP Source connector should display in the Paper:
  {{< figure src="../img/ftpsource-add-done.png" title="Add a FTP source name done" lightbox="true" height="100%" width="100%" >}}

- Now, hover over the FtpSource connector, a couple of buttons will show up. These are action buttons, let take a quick look and see what can we do with them (from left to right):
  {{< figure src="../img/ftpsource-add-action-buttons.png" title="Action buttons" lightbox="true" height="100%" width="100%" >}}

  - **Link**: create a new link, once a link is created you can move your mouse and the link will follow along your mouse position. To link to another component, you can hover over it and do a mouse right-click. To cancel the link creation, just click on the blank within the Paper.    
  - **Start**: start a component. Once the component is properly configured, you can then use this button to start the component.
  - **Stop**: stop a running or failed component
  - **Configure**: open the Property dialog and fill out necessary configuration for the component.
  - **Delete**: delete the selected component.

{{% alert note %}}
A **Link** can also be interacted with. You can remove it by clicking on the "x" button. 
And click on any point of the link creates a vertex. The vertex can be moved and tweaked to change its position. 
You can also delete a vertex by double clicking on it.
{{% /alert %}}

- Okay, that's enough of these button things. Let's click on the "configure" icon (a wrench) and fill in the following fields (Note that you need to use your own settings, and create completed, error, input and output directories). For fields that we did not mention below, the default is used:
  - Completed Folder: _demo/completed_
  - Error Folder: _demo/error_
  - Hostname of FTP Server: _10.2.0.28_
  - Port of FTP Server: _21_
  - User of FTP Server: _ohara_
  - Password of FTP Server: _oharastream_
  - Input Folder: _demo/input_

- Once these settings had been filled out, click on the **SAVE CHANGES** button.
  {{< figure src="../img/ftpsource-add-config.png" title="Add a FTP source configuration" lightbox="true" height="100%" width="100%" >}}
  
{{% alert hint %}}There are validation rules that prevent you from submitting the form without filling require fields.{{% /alert %}}
  
**FTP sink:**

Just like FTP source connector, we can drag and drop a FTP sink connector from the Toolbox and name it "ftpsink". 
After it's added in the Paper, click on its "configure" button. The settings are mostly like FTP source with the only exception: "output":

{{< figure src="../img/ftpsink-add-toolbox.png" title="Add a FTP sink" lightbox="true" height="100%" width="100%" >}}
{{< figure src="../img/ftpsink-config.png" title="Add a FTP sink configuration" lightbox="true" height="100%" width="100%" >}}
  
  - Hostname of FTP Server: _10.2.0.28_
  - Port of FTP Server: _21_
  - User of FTP Server: _ohara_
  - Password of FTP Server: _oharastream_
  - Output Folder: _demo/output_

Great! Now we have both source and sink connectors ready. Let's move on to create some topics.

{{< figure src="../img/ftpsink-add-done.png" title="Add a FTP sink done" lightbox="true" height="100%" width="100%" >}}

  
**Topic:**

In this tutorial, we need two topics for the pipeline, let's add them from the Toolbox like what we did in the previous steps for FTP connectors:

- From the Toolbox, click on the title "Topic" to expand the topic panel.
{{< figure src="../img/topic-add-toolbox.png" title="Add a topic from Toolbox" lightbox="true" height="100%" width="100%" >}}

- Click and drag "Pipeline Only" item from the list and drop it into the Paper to add a new Topic. Unlike source or sink connector, adding a topic doesn't require entering a name, the name will be auto-generated like (T1, T2, T3, etc.)
- Repeat the above step to create another Topic. You should now have two topics (T1, T2) in your Paper in addition to those FTP connectors:

{{< figure src="../img/topic-add-done.png" title="Add a topic done" lightbox="true" height="100%" width="100%" >}}

And luckily, there's no need to configure these topic as they're preconfigured and already running. (what a time saver!) 

{{% alert note %}}
  In Ohara, topics can either be a "Pipeline-only topic" or a "Shared topic". 
  The pipeline-only topics are topics that only live within a pipeline. 
  And on the other hand, shared topics can be shared and used across different pipelines. 
  For simplicity sake, we only use pipeline-only topics throughout the tutorial.
{{% /alert %}}

{{% alert tip %}}
  For quickly creating a new pipeline-only topic, you can also just add a source and a sink (or stream) connector and 
  then link them together with the "Link" button from the source connector component. 
  The pipeline-only topic will be created automatically.
{{% /alert %}}

**Stream:**

Stream is our last missing piece of the "pipeline". Let's add one very quick!

Remember the [stream jar](https://github.com/oharastream/ohara-quickstart/releases) you downloaded 
along with the quickstart image? It's time to use it:

- Click on the "Stream" panel on the Toolbox and then click on the "Add streams" button

{{< figure src="../img/add-stream-toolbox-upload.png" title="Upload a stream" lightbox="true" height="100%" width="100%" >}}

- It will open workspace settings and redirect you to the stream jars page:

{{< figure src="../img/add-stream-page.png" title="Add stream settings page" lightbox="true" height="100%" width="100%" >}}

- Click on the plus icon to open select file dialog:

{{< figure src="../img/add-stream-select-icon.png" title="Add stream select file icon" lightbox="true" height="100%" width="100%" >}}

- The workspace file list are currently empty, let's add a new file by clicking on the upload icon. This will open your OS file system, you can then select the stream jar file you downloaded. The stream class will be loaded and displayed in the list:

{{< figure src="../img/add-stream-upload-icon.png" title="Add stream upload icon" lightbox="true" height="100%" width="100%" >}}
{{< figure src="../img/add-stream-upload-done.png" title="Add stream select file upload done" lightbox="true" height="100%" width="100%" >}}

- Select the stream and click on the SAVE button to close select file dialog

{{< figure src="../img/add-stream-list.png" title="Add stream list" lightbox="true" height="100%" width="100%" >}}

- The stream file should now listed in your Stream Jars page. Close this page by clicking on the close button on the upper-right corner

{{< figure src="../img/add-stream-list-done.png" title="Add stream list done" lightbox="true" height="100%" width="100%" >}}

- Adding a stream is just like connector and topic, drag the "DumbStream" item from the Toolbox and drop it into the Paper and give a name "stream"

{{< figure src="../img/add-stream-name.png" title="Add stream name" lightbox="true" height="100%" width="100%" >}}

- Now let's work through the configuration together. Hover over the stream component and click on the "Configure" button to open the configure dialog
- Fill out the form with the following settings and click the "**SAVE CHANGES**" button:
  - The filtered value: _M_
  - The filtered header name: _Sex_
  - Node name list: _192.168.56.111_ (you should use you own IP)

{{< figure src="../img/add-stream-dialog.png" title="Add stream dialog" lightbox="true" height="100%" width="100%" >}}

{{% alert note %}}
This stream is capable of filtering out columns and values that we specified and then push 
the new result to a topic. Here we're specifically setting the "Sex" as the filtered header 
and "M" as the filtered value (stands for Man) and so our output data will only "include" 
data that contains "M" value in the "Sex" column. We will verify the result later in the tutorial.
{{% /alert %}}

Everything is ready. We can now create the connection like we mentioned earlier: 
{{ hl }} FTP source -> topic -> stream -> topic -> FTP sink {{ /hl }}. 
But before doing so, let's move these components a bit and so we can have more room 
to work with (You can close the Toolbox by clicking on the close icon on the top right corner):  

{{< figure src="../img/add-stream-change-layout.png" title="Add stream change layout" lightbox="true" height="100%" width="100%" >}}

Okay, it's time to create the connection:

- Hover over FTP source connector and click the "Link" button, and move your mouse to the first topic named "T1" and click on it. A connection should be created:

{{< figure src="../img/add-stream-create-first-link.png" title="Add stream create first link" lightbox="true" height="100%" width="100%" >}}

- Repeat the same step but this time with "T1" to create a connection from T1 to stream
- And stream -> T2 then T2 -> FTP sink connector. After you are done, you should have a graph like this (Components position have been tweaked so it's better to see the relation between these components):

{{< figure src="../img/add-stream-done.png" title="Add stream done" lightbox="true" height="100%" width="100%" >}}

{{% alert note %}}
You can also create the connection during configuring the connector or stream. For connector, just choose the topic from its topic list. For stream, you will need to choose both from and to topics from the topic list.
{{% /alert %}}


### Start pipeline components

So far so good, let's start all the components simply by clicking on the "Start all components" 
button located on the Toolbar menu. If everything goes well you should see that all components' 
icon are now green just like the following image:

{{< figure src="../img/start-components-done.png" title="Start components done" lightbox="true" height="100%" width="100%" >}}


### Test our new pipeline

Let's test this "pipeline" to see if it's capable of transferring some data. We have 
prepared a CSV file which looks like this (you can download this file from 
[here](https://people.sc.fsu.edu/~jburkardt/data/csv/freshman_kgs.csv)):

{{< figure src="../img/csv.png" title="CSV file" lightbox="true" height="100%" width="100%" >}}

Upload the file to the FTP service's input folder. Wait a while, the file should be 
consumed and move to the output folder. You can verify if the data is properly transferred 
by using a FTP client to check the file (we're using [FileZilla](https://filezilla-project.org/)).

{{< figure src="../img/csv-output.png" title="CSV output" lightbox="true" height="100%" width="100%" >}}

The output data should be filtered with the result only "M" (man) data are listed as shown below:

{{< figure src="../img/csv-output-done.png" title="CSV output done" lightbox="true" height="100%" width="100%" >}}


## Troubleshooting

We now provide a few debugging tools that can help you pin down unexpected errors:

### Event log panel

All UI events are recorded, things like API request and response are also stored. 
You can view all you event log by simply opening up the Event log panel. As you can 
see in the screenshot, errors are highlighted and have more details that can be viewed 
when click on each of them.

{{< figure src="../img/event-log-icon.png" title="Event log icon" lightbox="true" height="100%" width="100%" >}}
{{< figure src="../img/event-log-list.png" title="Event log list" lightbox="true" height="100%" width="100%" >}}
{{< figure src="../img/event-log-dialog.png" title="Event log dialog" lightbox="true" height="100%" width="100%" >}}

Another thing that is worth mentioning here is the Event log icon will display the error log's count on the icon

{{< figure src="../img/event-log-notification.png" title="Event log notification" lightbox="true" height="100%" width="100%" >}}

### Developer Tools panel

Open the dev tool from the App bar:

{{< figure src="../img/devtool-icon.png" title="DevTool icon" lightbox="true" height="100%" width="100%" >}}
  
There are two main functionalities that could be utilized here:

- Topic panel: you can quickly preview the topic data here

{{< figure src="../img/devtool-topic-data.png" title="DevTool topic data" lightbox="true" height="100%" width="100%" >}}

- Log panel: view all running service's full log

{{< figure src="../img/devtool-topic-log.png" title="DevTool topic log" lightbox="true" height="100%" width="100%" >}}

### Delete workspace

Deleting a workspace is a new feature implemented in 0.10.0, with this feature, you can now delete a workspace with our UI

{{% alert note %}}
All pipelines under the workspace that you're about to delete should be stopped 
(in other word, everything except topics in the pipeline should have the status "stopped", 
you can do so by going to each pipeline's Toolbar and click on the "Stop all components" 
item from the Pipeline actions
{{% /alert %}}

- From Navigator, click on the workspace name, and click on the "Settings" item from that dropdown:

{{< figure src="../img/delete-workspace-menu.png" title="Delete workspace menu" lightbox="true" height="100%" width="100%" >}}
  
- In the Settings dialog, scroll to the very bottom of the page, and click "Delete this workspace"

{{< figure src="../img/delete-workspace-settings-button.png" title="Delete workspace settings button" lightbox="true" height="100%" width="100%" >}}
  
- A confirm dialog will pop up. Enter the workspace name and click on the DELETE button to start deleting. (Note that if you still have some services running in the workspace, you won't able to proceed. You should following the instruction mention in the above to stop all pipelines first)

{{< figure src="../img/delete-workspace-confirm.png" title="Delete workspace confirm dialog" lightbox="true" height="100%" width="100%" >}}

- The deletion is in progress, after the deletion is completed, you will be redirected to home or a default workspace if you have one.

{{< figure src="../img/delete-workspace-progress.png" title="Delete workspace progress dialog" lightbox="true" height="100%" width="100%" >}}

### Restart workspace

When new changes were made in workspace, the restart is required. Also, if you ever run into issues that cannot be recovered from, you can try to restart the workspace to fix the issue.

Since delete workspace and restart workspace are almost identical, the following instruction won't include any screenshots as they are already included in the [Delete workspace](#delete-workspace) section

- From Navigator, click on the workspace name, and click on the "Settings" item from that dropdown.
- In the Settings dialog, scroll to the very bottom of the page, and click "Restart this workspace".
- A confirm dialog will pop up. Click on the RESTART button to start restarting this workspace.
- The restarting is in progress, after the restart is completed, you can close the progress 
  dialog and changes you made should applied to the workspace by now.

{{% alert warning %}} Same with delete a workspace, you will need to make sure all pipelines are stopped before starting: {{% /alert %}}


And if you think you ever encountered a bug, let us know: [GitHub Repo](https://github.com/oharastream/ohara/issues/).
