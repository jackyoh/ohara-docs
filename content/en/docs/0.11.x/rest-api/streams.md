---
title: Stream
linktitle: Stream API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  011x:
    parent: REST APIs
    weight: 160
---

Ohara Stream is a unparalleled wrap of kafka streaming. It leverages and
enhances [Kafka Streams](https://kafka.apache.org/documentation/streams)
to make developer easily design and implement the streaming application.
More details of developing streaming application is in
[custom stream guideline]({{< relref "../custom_stream.md" >}}).

Assume that you have completed a streaming application via Ohara Java
APIs, and you have generated a jar including your streaming code. By
Ohara Restful APIs, you are enable to control, deploy, and monitor your
streaming application. As with cluster APIs, Ohara leverages docker
container to host streaming application. Of course, you can apply your
favor container management tool including simple (based on ssh) and k8s
when you are starting Ohara.

Before stating to use restful APIs, please ensure that all nodes have
downloaded the [Stream image](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/stream).
The jar you uploaded to run streaming application will be included in
the image and then executes as a docker container. 
The [Stream image](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/stream)
is kept in each node so don't worry about the network. We all hate
re-download everything when running services.

{{% alert hint %}}
If you implement the Ohara stream application, you must pack to jar
file and use the File API upload jar file then setting the jarKey to
create the Stream API.
{{% /alert %}}

The following information of Stream are updated by Ohara.

## stream stored data {#stored-data}

The following are common settings to a stream app.

1. name (**string**) --- cluster name. The legal character is number,
   lowercase alphanumeric characters, or '.'
2. group (**string**) --- cluster group. The legal character is number,
   lowercase alphanumeric characters, or '.'
3. jarKey (**object**) --- the used jar key
4. jmxPort (**int**) --- expose port for jmx
5. className (**string**) --- the class to be executed. This field is optional 
   and Configurator will pick up a class from the input jar. However, it throw 
   exception if there are many available classes in the jar file.
6. from (**array(TopicKey)**) --- source topic
7. to (**array(TopicKey)**) --- target topic
8. nodeNames (**array(string)**) --- the nodes running the stream
   process
9. brokerClusterKey (**object**) --- the broker cluster key used for
   stream running
   - brokerClusterKey.group (**option(string)**) --- the group of
     broker cluster
   - brokerClusterKey.name (**string**) --- the name of broker
     cluster
{{% alert note %}}
the following forms are legal as well: (1) `{"name": "n"}`, (2) `"n"`.
Both forms are converted to `{"group": "default", "name": "n"}`
{{% /alert %}}
10. tags (**object**) --- the user defined parameters
11. aliveNodes (**array(string)**) --- the nodes that host the running
    containers of stream cluster
12. state (**option(string)**) --- only started/failed stream has state
    (DEAD if all containers are not running, else RUNNING)
13. error (**option(string)**) --- the error message from a failed
    stream. If the stream is fine or un-started, you won't get this
    field.
14. [nodeMetrics]({{< relref "../custom_connector.md#metrics" >}}) (**object**) --- 
    the metrics from this stream.
    - meters (**array(object)**) --- the metrics in meter type
      - meters[i].value (**double**) --- the number stored in meter
      - meters[i].unit (**string**) --- unit for value
      - meters[i].document (**string**) --- document of this meter
      - meters[i].queryTime (**long**) --- the time of query
        metrics from remote machine
      - meters[i].startTime (**option(long)**) --- the time of
        record generated in remote machine
15. lastModified (**long**) --- last modified this jar time

## create properties of specific stream {#create}

Create the properties of a stream.

*POST /v0/streams*

1. name (**string**) --- new stream name. This is the object unique
   name ; default is random string.
2. group (**string**) --- group name for current stream ; default value
   is "default"
3. imageName (**string**) --- image name of stream used to ; default is
   oharastream/stream:{{< ohara-version >}}
4. nodeNames (**array(string)**) --- node name list of stream used to ;
   default is empty
5. tags (**object**) --- a key-value map of user defined data ; default
   is empty
6. jarKey (**object**) --- the used jar key
   - group (**string**) --- the group name of this jar
   - name (**string**) --- the name of this jar
7. brokerClusterKey (**option(object)**) --- the broker cluster used
   for stream running ; default we will auto fill this parameter for
   you if you don't specify it and there only exists one broker
   cluster.
8. jmxPort (**int**) --- expose port for jmx ; default is random port
9. from (**array(TopicKey)**) --- source topic ; default is empty array

{{% alert hint %}}
Currently, stream uses a "single flow" for users to define their
own data flow, we need a better structure to support multiple
topics.

We only support one topic for current version. We will throw
exception in start api if you assign more than 1 topic. We will
support multiple topics on issue [#688]({{< ohara-issue 688 >}})

{{% /alert %}}

10. to (**array(TopicKey)**) --- target topic ; default is empty array

{{% alert hint %}}
Currently, stream uses a "single flow" for users to define their
own data flow, we need a better structure to support multiple
topics.

We only support one topic for current version. We will throw
exception in start api if you assign more than 1 topic. We will
support multiple topics on issue [#688]({{< ohara-issue 688 >}})
{{% /alert %}}

* Example Request
    ```json
    {
      "name": "streamtest1",
      "brokerClusterKey": "bk",
      "jarKey": "ohara-it-stream.jar",
      "nodeNames": ["node00"],
      "from": ["topic0"],
      "to": ["topic1"]
    }
    ```
  
* Example Response

    Response format is as [stream stored format]({{< relref "#stored-data" >}}).

    ```json
    {
      "author": "root",
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "streamtest1",
      "xms": 1024,
      "routes": {},
      "lastModified": 1579145546218,
      "tags": {},
      "xmx": 1024,
      "imageName": "oharastream/stream:{{< ohara-version >}}",
      "jarKey": {
        "group": "default",
        "name": "ohara-it-stream.jar"
      },
      "to": [
        {
          "group": "default",
          "name": "topic1"
        }
      ],
      "revision": "b303f3c2e52647ee5e79e55f9d74a5e51238a92c",
      "version": "{{< ohara-version >}}",
      "aliveNodes": [],
      "stream.class": "oharastream.ohara.it.stream.DumbStream",
      "from": [
        {
          "group": "default",
          "name": "topic0"
        }
      ],
      "nodeMetrics": [],
      "jmxPort": 44914,
      "kind": "stream",
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

{{% alert hint %}}
The stream, which is just created, does not have any metrics.
{{% /alert %}}

## get information from a specific stream cluster {#get}

*GET /v0/streams/${name}?group=$group*

{{% alert hint %}}
We will use the default value as the query parameter "?group=" if you
don't specify it.
{{% /alert %}}

* Example Response

    Response format is as [stream stored format]({{< relref "#stored-data" >}}).

    ```json
    {
      "author": "root",
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "streamtest1",
      "xms": 1024,
      "routes": {},
      "lastModified": 1579145546218,
      "tags": {},
      "xmx": 1024,
      "imageName": "oharastream/stream:{{< ohara-version >}}",
      "jarKey": {
        "group": "default",
        "name": "ohara-it-stream.jar"
      },
      "to": [
        {
          "group": "default",
          "name": "topic1"
        }
      ],
      "revision": "b303f3c2e52647ee5e79e55f9d74a5e51238a92c",
      "version": "{{< ohara-version >}}",
      "aliveNodes": [],
      "stream.class": "oharastream.ohara.it.stream.DumbStream",
      "from": [
        {
          "group": "default",
          "name": "topic0"
        }
      ],
      "nodeMetrics": [],
      "jmxPort": 44914,
      "kind": "stream",
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## list information of stream cluster

*GET /v0/streams*

the accepted query keys are listed below

1. author
2. group
3. name
4. lastModified
5. tags
6. state
7. aliveNodes
8. key
9. version

* Example Response

    Response format is as [stream stored format]({{< relref "#stored-data" >}}).

    ```json
    [
      {
        "author": "root",
        "brokerClusterKey": {
          "group": "default",
          "name": "bk"
        },
        "name": "streamtest1",
        "xms": 1024,
        "routes": {},
        "lastModified": 1579145546218,
        "tags": {},
        "xmx": 1024,
        "imageName": "oharastream/stream:{{< ohara-version >}}",
        "jarKey": {
          "group": "default",
          "name": "ohara-it-stream.jar"
        },
        "to": [
          {
            "group": "default",
            "name": "topic1"
          }
        ],
        "revision": "b303f3c2e52647ee5e79e55f9d74a5e51238a92c",
        "version": "{{< ohara-version >}}",
        "aliveNodes": [],
        "stream.class": "oharastream.ohara.it.stream.DumbStream",
        "from": [
          {
            "group": "default",
            "name": "topic0"
          }
        ],
        "nodeMetrics": [],
        "jmxPort": 44914,
        "kind": "stream",
        "group": "default",
        "nodeNames": [
          "node00"
        ]
      }
    ]
    ```

## update properties of specific stream {#update}

Update the properties of a non-started stream.

*PUT /v0/streams/${name}?group=$group*

{{% alert hint %}}
If the required stream (group, name) was not exists, we will try to use
this request as [create stream]({{< relref "#create" >}})
{{% /alert %}}

1. imageName (**option(string)**) --- image name of stream used to.
2. nodeNames (**option(array(string))**) --- node name list of stream
   used to.
3. tags (**option(object)**) --- a key-value map of user defined data.
4. jarKey (**option(option(object))**) --- the used jar key
   - group (**option(string)**) --- the group name of this jar
   - name (**option(string)**) --- the name without extension of this
     jar
5. jmxPort (**option(int)**) --- expose port for jmx.
6. from (**option(array(string))**) --- source topic.

{{% alert hint %}}
Currently, stream uses a "single flow" for users to define their
own data flow, we need a better structure to support multiple
topics.

we only support one topic for current version. We will throw
exception in start api if you assign more than 1 topic. We will
support multiple topics on issue [#688]({{< ohara-issue 688 >}})
{{% /alert %}}

7. to (**option(array(string))**) --- target topic.

{{% alert hint %}}
Currently, stream uses a "single flow" for users to define their
own data flow, we need a better structure to support multiple
topics.

we only support one topic for current version. We will throw
exception in start api if you assign more than 1 topic. We will
support multiple topics on issue [#688]({{< ohara-issue 688 >}})
{{% /alert %}}

* Example Request
    ```json
    {
      "from": ["topic2"],
      "to": ["topic3"],
      "jarKey": "ohara-it-stream.jar",
      "nodeNames": ["node01"]
    }
    ```

* Example Response

    Response format is as [stream stored format]({{< relref "#stored-data" >}}).

    ```json
    {
      "author": "root",
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "streamtest1",
      "xms": 1024,
      "routes": {},
      "lastModified": 1579153777586,
      "tags": {},
      "xmx": 1024,
      "imageName": "oharastream/stream:{{< ohara-version >}}",
      "jarKey": {
        "group": "default",
        "name": "ohara-it-stream.jar"
      },
      "to": [
        {
          "group": "default",
          "name": "topic3"
        }
      ],
      "revision": "b303f3c2e52647ee5e79e55f9d74a5e51238a92c",
      "version": "{{< ohara-version >}}",
      "aliveNodes": [],
      "stream.class": "oharastream.ohara.it.stream.DumbStream",
      "from": [
        {
          "group": "default",
          "name": "topic2"
        }
      ],
      "nodeMetrics": [],
      "jmxPort": 44914,
      "kind": "stream",
      "group": "default",
      "nodeNames": [
        "node01"
      ]
    }
    ```

## delete properties of specific stream

Delete the properties of a non-started stream. This api only remove the
stream component which is stored in pipeline.

*DELETE /v0/streams/${name}?group=$group*

{{% alert hint %}}
We will use the default value as the query parameter "?group=" if you
don't specify it.
{{% /alert %}}

* Example Response
    ```
    204 NoContent
    ```

{{% alert hint %}}
It is ok to delete an nonexistent properties, and the response is
204 NoContent.
{{% /alert %}}

## start a Stream

*PUT /v0/streams/${name}/start?group=$group*

{{% alert hint %}}
We will use the default value as the query parameter "?group=" if you
don't specify it.
{{% /alert %}}

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [get stream]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}

## stop a Stream {#stop}

This action will graceful stop and remove all docker containers belong
to this stream. Note: successful stop stream will have no status.

*PUT /v0/streams/${name}/stop?group=$group[&force=true]*

* Query Parameters
    1. force (**boolean**) --- true if you don't want to wait the
       graceful shutdown (it can save your time but may damage your
       data).

{{% alert hint %}}
We will use the default value as the query parameter "?group=" if you
don't specify it.
{{% /alert %}}

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [get stream]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}

## get topology tree graph from specific stream

[TODO] This is not implemented yet !

*GET /v0/streams/view/${name}*

* Example Response
  1. jarInfo (**string**) --- the upload jar information
  2. name (**string**) --- the stream name
  3. poneglyph (**object**) --- the stream topology tree graph
     - steles (**array(object)**) --- the topology collection
       - steles[i].kind (**string**) --- this component
         kind (SOURCE, PROCESSOR, or SINK)
       - steles[i].key (**string**) --- this component kind with order
       - steles[i].name (**string**) --- depend on kind, the name is
         - SOURCE --- source topic name
         - PROCESSOR --- the function name
         - SINK --- target topic name
       - steles[i].from (**string**) --- the prior
         component key (could be empty if this is the first
         component)
       - steles[i].to (**string**) --- the posterior
         component key (could be empty if this is the final
         component)

    ```json
    {
      "jarInfo": {
        "name": "stream-app",
        "group": "wk01",
        "size": 1234,
        "lastModified": 1542102595892
      },
      "name": "my-app",
      "poneglyph": {
        "steles": [
          {
            "kind": "SOURCE",
            "key" : "SOURCE-0",
            "name": "stream-in",
            "from": "",
            "to": "PROCESSOR-1"
          },
          {
            "kind": "PROCESSOR",
            "key" : "PROCESSOR-1",
            "name": "filter",
            "from": "SOURCE-0",
            "to": "PROCESSOR-2"
          },
          {
            "kind": "PROCESSOR",
            "key" : "PROCESSOR-2",
            "name": "mapvalues",
            "from": "PROCESSOR-1",
            "to": "SINK-3"
          },
          {
            "kind": "SINK",
            "key" : "SINK-3",
            "name": "stream-out",
            "from": "PROCESSOR-2",
            "to": ""
          }
        ]
      }
    }
    ```
