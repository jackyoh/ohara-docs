---
title: Connector
linktitle: Connector API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  011x:
    parent: REST APIs
    weight: 70
---

Connector is core of application in Ohara
[pipeline]({{< relref "./pipelines.md" >}}). Connector has
two type - source and sink. Source connector pulls data from another
system and then push to topic. By contrast, Sink connector pulls data
from topic and then push to another system. In order to use connector in
[pipeline]({{< relref "./pipelines.md" >}}), you have to
set up a connector settings in Ohara and then add it to
[pipeline]({{< relref "./pipelines.md" >}}). Of course,
the connector settings must belong to a existent connector in target
worker cluster. By default, worker cluster hosts only the official
connectors. If you have more custom requirement for connector, please
follow [custom connector guideline]({{< relref "../custom_connector.md" >}})
to write your connector.

Apart from custom settings, common settings are required by all
connectors. The common settings are shown below.

1. group (**string**) --- the value of group is always "default". The
   legal character is number, lowercase alphanumeric characters, or '.'
2. name (**string**) --- the name of this connector. The legal
   character is number, lowercase alphanumeric characters, or '.'
3. connector.class (**class**) --- class name of connector
   implementation
4. topicKeys(**array(object)**) --- the source topics or target topics
   for this connector
5. columns (**array(object)**) --- the schema of data for this
   connector
   - columns[i].name (**string**) --- origin name of column
   - columns[i].newName (**string**) --- new name of column
   - columns[i].dataType (**string**) --- the type used to convert
     data
   - columns[i].order (**int**) --- the order of this column
6. numberOfTasks (**int**) --- the number of tasks
7. workerClusterKey (**Object**) --- target worker cluster.
   - workerClusterKey.group (**option(string)**) --- the group of
     cluster
   - workerClusterKey.name (**string**) --- the name of cluster

{{% alert note %}}
the following forms are legal as well: (1) `{"name": "n"}`, (2) `"n"`.
Both forms are converted to `{"group": "default", "name": "n"}`
{{% /alert %}}
8. tags (**object**) - the extra description to this object

The following information are updated by Ohara:

1. group (**string**) --- connector's group
2. name (**string**) --- connector's name
3. lastModified (**long**) --- the last time to update this connector
4. state (**option(string)**) --- the state of a started connector
5. aliveNodes (**Set(string)**) --- the nodes hosting this connector
6. error (**option(string)**) --- the error message from a failed
   connector. If the connector is fine or un-started, you won't get
   this field.
7. tasksStatus (**Array(object)**) --- the tasks status of this connector
   - tasksStatus[i].state (**string**) --- the state of a started task.
   - tasksStatus[i].nodeName (**string**) --- the node hosting this task
   - tasksStatus[i].error (**option(string)**) --- the error
     message from a failed task. If the task is fine or un-started,
     you won't get this field.
   - tasksStatus[i].coordinator (**boolean**) --- true if this status
     is coordinator. otherwise, false
8. [nodeMetrics]({{< relref "../custom_connector.md#metrics" >}}) (**object**) --- 
   the metrics from a running connector
   - meters (**array(object)**) --- the metrics in meter type
     - meters[i].name (**string**) --- the number of this meter
       (normally, it is unique)
     - meters[i].value (**double**) --- the value in double
     - meters[i].valueInPerSec (**double**) --- the average value
       in per second
     - meters[i].unit (**string**) --- the unit of value
     - meters[i].document (**string**) --- human-readable
       description to this meter
     - meters[i].queryTime (**Long**) --- the time we query this
       meter from remote nodes
     - meters[i].startTime (**Long**) --- the time to start this
       meter (not all services offer this record)
     - meters[i].lastModified (**Long**) --- the time of modifying
       metrics

The following keys are internal and protected so you can't define them
in creating/updating connector.

1. connectorKey --- It points to the really (group, name) for the
   connector running in kafka.
2. topics --- It points to the really topic names in kafka for the
   connector running in kafka.

## create the settings of connector {#create-settings}

*POST /v0/connectors*

It is ok to lack some common settings when creating settings for a
connector. However, it is illegal to start a connector with incomplete
settings. For example, storing the settings consisting of only
**connector.name** is ok. But stating a connector with above incomplete
settings will introduce a error.

* Example Request
    ```json
    {
      "name":"perf",
      "topicKeys": ["t0"],
      "workerClusterKey": "wk",
      "connector.class":"oharastream.ohara.connector.perf.PerfSource"
    }
    ```

* Example Response
    ```json
    {
      "header.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "author": "root",
      "topicKeys": [
        {
          "group": "default",
          "name": "t0"
        }
      ],
      "name": "perf",
      "check.rule": "NONE",
      "key.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "lastModified": 1577282907085,
      "tags": {},
      "value.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "perf.cell.length": 10,
      "tasks.max": 1,
      "perf.batch": 10,
      "perf.frequency": "1000 milliseconds",
      "connector.class": "oharastream.ohara.connector.perf.PerfSource",
      "revision": "baafe4a3d875e5e5028b686c4f74f26cfd8b1b66",
      "version": "{{< ohara-version >}}",
      "columns": [],
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "number of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "messages",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "size (in bytes) of messages",
              "lastModified": 1585068870445,
              "name": "message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 8.19825E+8,
              "valueInPerSec": 19094561.546523817
            },
            {
              "document": "size of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "number of messages",
              "lastModified": 1585068870445,
              "name": "message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827508,
              "unit": "messages",
              "value": 1275000.0,
              "valueInPerSec": 29694.66893355381
            }
          ]
        }
      },
      "workerClusterKey": {
        "group": "default",
        "name": "wk"
      },
      "tasksStatus": [],
      "kind": "source",
      "group": "default"
    }
    ```

## update the settings of connector

*PUT /v0/connectors/${name}?group=${group}*

{{% alert hint %}}
you cannot update a non-stopped connector.
{{% /alert %}}

* Example Request
    ```json
    {
      "topicKeys": [
        "t1"
      ]
    }
    ```

* Example Response
    ```json
    {
      "header.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "author": "root",
      "topicKeys": [
        {
          "group": "default",
          "name": "t1"
        }
      ],
      "name": "perf",
      "check.rule": "NONE",
      "key.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "lastModified": 1577283010533,
      "tags": {},
      "value.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "perf.cell.length": 10,
      "tasks.max": 1,
      "perf.batch": 10,
      "perf.frequency": "1000 milliseconds",
      "connector.class": "oharastream.ohara.connector.perf.PerfSource",
      "revision": "baafe4a3d875e5e5028b686c4f74f26cfd8b1b66",
      "version": "{{< ohara-version >}}",
      "columns": [],
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "number of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "messages",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "size (in bytes) of messages",
              "lastModified": 1585068870445,
              "name": "message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 8.19825E+8,
              "valueInPerSec": 19094561.546523817
            },
            {
              "document": "size of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "number of messages",
              "lastModified": 1585068870445,
              "name": "message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827508,
              "unit": "messages",
              "value": 1275000.0,
              "valueInPerSec": 29694.66893355381
            }
          ]
        }
      },
      "workerClusterKey": {
        "group": "default",
        "name": "wk"
      },
      "tasksStatus": [],
      "kind": "source",
      "group": "default"
    }
    ```

## list information of all connectors

*GET /v0/connectors*

the accepted query keys are listed below. 
- group 
- name 
- lastModified 
- tags 
- tag - this field is similar to tags but it addresses the "contain" behavior. 
- key

* Example Response
    ```json
    [
      {
        "header.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
        "author": "root",
        "topicKeys": [
          {
            "group": "default",
            "name": "t1"
          }
        ],
        "name": "perf",
        "check.rule": "NONE",
        "key.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
        "lastModified": 1577283010533,
        "tags": {},
        "value.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
        "perf.cell.length": 10,
        "tasks.max": 1,
        "perf.batch": 10,
        "perf.frequency": "1000 milliseconds",
        "connector.class": "oharastream.ohara.connector.perf.PerfSource",
        "revision": "baafe4a3d875e5e5028b686c4f74f26cfd8b1b66",
        "version": "{{< ohara-version >}}",
        "columns": [],
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "number of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "messages",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "size (in bytes) of messages",
              "lastModified": 1585068870445,
              "name": "message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 8.19825E+8,
              "valueInPerSec": 19094561.546523817
            },
            {
              "document": "size of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "number of messages",
              "lastModified": 1585068870445,
              "name": "message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827508,
              "unit": "messages",
              "value": 1275000.0,
              "valueInPerSec": 29694.66893355381
            }
          ]
        }
      },
        "workerClusterKey": {
          "group": "default",
          "name": "wk"
        },
        "tasksStatus": [],
        "kind": "source",
        "group": "default"
      }
    ]
    ```

## delete a connector {#delete}

*DELETE /v0/connectors/${name}?group=${group}*

Deleting the settings used by a running connector is not allowed. You
should [stop]({{< relref "#stop" >}}) connector before deleting it.

* Example Response
    
    ````
    204 NoContent
    ````
    
    
{{% alert hint %}}
It is ok to delete an jar from an nonexistent connector or a running
connector, and the response is 204 NoContent.
{{% /alert %}}

## get information of connector {#get-info}

*GET /v0/connectors/${name}?group=${group}*

* Example Response
    ```json
    {
      "header.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "author": "root",
      "topicKeys": [
        {
          "group": "default",
          "name": "t1"
        }
      ],
      "name": "perf",
      "check.rule": "NONE",
      "key.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "lastModified": 1577283010533,
      "tags": {},
      "value.converter": "org.apache.kafka.connect.converters.ByteArrayConverter",
      "perf.cell.length": 10,
      "tasks.max": 1,
      "perf.batch": 10,
      "perf.frequency": "1000 milliseconds",
      "connector.class": "oharastream.ohara.connector.perf.PerfSource",
      "revision": "baafe4a3d875e5e5028b686c4f74f26cfd8b1b66",
      "version": "{{< ohara-version >}}",
      "columns": [],
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "number of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "messages",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "size (in bytes) of messages",
              "lastModified": 1585068870445,
              "name": "message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 8.19825E+8,
              "valueInPerSec": 19094561.546523817
            },
            {
              "document": "size of ignored messages",
              "lastModified": 1585068827510,
              "name": "ignored.message.size",
              "queryTime": 1585068870341,
              "startTime": 1585068827510,
              "unit": "bytes",
              "value": 0.0,
              "valueInPerSec": 0.0
            },
            {
              "document": "number of messages",
              "lastModified": 1585068870445,
              "name": "message.number",
              "queryTime": 1585068870341,
              "startTime": 1585068827508,
              "unit": "messages",
              "value": 1275000.0,
              "valueInPerSec": 29694.66893355381
            }
          ]
        }
      },
      "workerClusterKey": {
        "group": "default",
        "name": "wk"
      },
      "tasksStatus": [],
      "kind": "source",
      "group": "default"
    }
    ```

## start a connector

*PUT /v0/connectors/${name}/start?group=${group}*

Ohara will send a start request to specific worker cluster to start the
connector with stored settings, and then make a response to called. The
connector is executed async so the connector may be still in starting
after you retrieve the response. You can send [GET request]({{< relref "#get-info" >}})
to see the state of connector. This request is idempotent so it is safe
to retry this command repeatedly.

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use
[Get Connector info]({{< relref "#get-info" >}}) to fetch up-to-date status
{{% /alert %}}

## stop a connector {#stop}

*PUT /v0/connectors/${name}/stop?group=${group}*

Ohara will send a stop request to specific worker cluster to stop the
connector. The stopped connector will be removed from worker cluster.
The settings of connector is still kept by Ohara so you can start the
connector with same settings again in the future. If you want to delete
the connector totally, you should stop the connector and then
[delete]({{< relref "#delete" >}}) it. This
request is idempotent so it is safe to send this request repeatedly.

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get Connector info]({{< relref "#get-info" >}}) to fetch up-to-date status
{{% /alert %}}

## pause a connector

*PUT /v0/connectors/${name}/pause?group=${group}*

Pausing a connector is to disable connector to pull/push data from/to
source/sink. The connector is still alive in kafka. This request is
idempotent so it is safe to send this request repeatedly.

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get Connector info]({{< relref "#get-info" >}}) to fetch up-to-date status
{{% /alert %}}

## resume a connector

*PUT /v0/connectors/${name}/resume?group=${group}*

Resuming a connector is to enable connector to pull/push data from/to
source/sink. This request is idempotent so it is safe to retry this
command repeatedly.

* Example Response
    ```
    202 Accepted
    ```
  
{{% alert hint %}}
You should use [Get Connector info]({{< relref "#get-info" >}}) to fetch up-to-date status
{{% /alert %}}
