---
title: Pipeline
linktitle: Pipeline
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 140
oharaVersion: 0.10.0
---

Pipeline APIs are born of Ohara manager which needs a way to store the
relationship of components in streaming. The relationship in pipeline is
made up of multi **endpoints**. Each **endpoint** describe a component.
For example, you have a [topic]({{< relref "topics.md" >}})
as source so you can describe the relationship via following
endpoints.

```json
{
  "endpoints": [
    {
      "group": "default",
      "name": "topic0",
      "kind": "topic"
    }
  ]
}
```

The objects grouped by pipeline should be existent. Otherwise, pipeline
will ignore them in generating object abstracts.

The objects grouped by pipeline don't need to located on the same
cluster hierarchy. Grouping a topic, which is placed at broker_0, and a
topic, which is located at broker_1, is valid. However, the object
based on a dead cluster will get an abstract with error state.

The properties used in generating pipeline are shown below.

1. group (**string**) --- pipeline's group. The legal character is
   number, lowercase alphanumeric characters, or '.'
2. name (**string**) --- pipeline's name. The legal character is
   number, lowercase alphanumeric characters, or '.'
3. endpoints (**array(object)**) --- the relationship between objects
   - endpoints[i].group (**String**) --- the group of this endpoint
   - endpoints[i].name (**String**) --- the name of this endpoint
   - endpoints[i].kind (**String**) --- the kind of this endpoint
4. tags (**object**) --- the extra description to this object

Following information are written by Ohara.

1. lastModified (**long**) --- the last time to update this pipeline
2. objects (**array(object)**) --- the abstract of all objects
   mentioned by pipeline
   - objects[i].name (**string**) --- object's name
   - objects[i].kind (**string**) --- the type of this object. for
     instance, [topic]({{< relref "topics.md" >}}),
     [connector]({{< relref "connectors.md" >}}), and
     [stream]({{< relref "streams.md" >}})
   - objects[i].className (**string**) --- object's implementation.
     Normally, it shows the full name of a java class
   - objects[i].state (**option(string)**) --- the state of object.
     If the object can't have state (eg, [topic]({{< relref "topics.md" >}})), 
     you won't see this field
   - objects[i].error (**option(string)**) --- the error message of
     this object
   - objects[i].lastModified (**long**) --- the last time to update
     this object
   - [nodeMetrics]({{< relref "../custom_connector.md#metrics" >}}) (**object**) --- 
     the metrics from this object. Not all objects in pipeline have metrics!
   - meters (**array(object)**) --- the metrics in meter type
   - meters[i].value (**double**) --- the number stored in meter
   - meters[i].unit (**string**) --- unit for value
   - meters[i].document (**string**) --- document of this meter
   - meters[i].queryTime (**long**) --- the time of query metrics
     from remote machine
   - meters[i].startTime (**option(long)**) --- the time of record
     generated in remote machine
3. jarKeys (**array(object)**) --- the jars used by the objects in
   pipeline.

## create a pipeline

*POST /v0/pipelines*

The following example creates a pipeline with a
[topic]({{< relref "topics.md" >}}) and [connector]({{< relref "connectors.md" >}}). 
The [topic]({{< relref "topics.md" >}}) is created on [broker cluster]({{< relref "brokers.md" >}}) 
but the [connector]({{< relref "connectors.md" >}}) isn't.
Hence, the response from server shows that it fails to find the status
of the [connector]({{< relref "connectors.md" >}}). That
is to say, it is ok to add un-running [connector]({{< relref "connectors.md" >}}) to pipeline.

Allow setting the not exists object for the endpoint. The object
resposne value is empty.

* Example Request 1 - Running single topic example
    ```json
    {
      "name": "pipeline0",
      "endpoints": [
        {
          "group": "default",
          "name": "topic0",
          "kind": "topic"
        }
      ]
    }
    ```

* Example Response 1
    ```json
    {
      "name": "pipeline0",
      "lastModified": 1578639344607,
      "endpoints": [
        {
          "group": "default",
          "name": "topic0",
          "kind": "topic"
        }
      ],
      "tags": {},
      "objects": [
        {
          "name": "topic0",
          "state": "RUNNING",
          "lastModified": 1578635914746,
          "tags": {},
          "nodeMetrics": [],
          "kind": "topic",
          "group": "default"
        }
      ],
      "jarKeys": [],
      "group": "default"
    }
    ```

* Example Request 2 - Running topic and perf connector example
    ```json
    {
      "name": "pipeline1",
      "endpoints": [
        {
          "group": "default",
          "name": "topic0",
          "kind": "topic"
        },
        {
          "group": "default",
          "name": "perf",
          "kind": "connector"
        }
      ]
    }
    ```

* Example Response 2
    ```json
    {
      "name": "pipeline1",
      "lastModified": 1578649709850,
      "endpoints": [
        {
          "group": "default",
          "name": "topic0",
          "kind": "topic"
        },
        {
          "group": "default",
          "name": "perf",
          "kind": "connector"
        }
      ],
      "tags": {},
      "objects": [
        {
          "name": "topic0",
          "state": "RUNNING",
          "lastModified": 1578649564486,
          "tags": {},
          "nodeMetrics": [],
          "kind": "topic",
          "group": "default"
        },
        {
          "name": "perf",
          "state": "RUNNING",
          "lastModified": 1578649620960,
          "tags": {},
          "className": "oharastream.ohara.connector.perf.PerfSource",
          "nodeMetrics": [],
          "kind": "source",
          "group": "default"
        }
      ],
      "jarKeys": [],
      "group": "default"
    }
    ```

## update a pipeline

*PUT /v0/pipelines/$name*

* Example Request
    ```json
    {
      "endpoints": [
        {
          "group": "default",
          "name": "topic1",
          "kind": "topic"
        }
      ]
    }
    ```

{{% alert hint %}}
This API creates a new pipeline for you if the input name does not exist!
{{% /alert %}}

* Example Response
    ```json
    {
      "name": "pipeline0",
      "lastModified": 1578641282237,
      "endpoints": [
        {
          "group": "default",
          "name": "topic1",
          "kind": "topic"
        }
      ],
      "tags": {},
      "objects": [
        {
          "name": "topic1",
          "state": "RUNNING",
          "lastModified": 1578641231579,
          "tags": {},
          "nodeMetrics": [],
          "kind": "topic",
          "group": "default"
        }
      ],
      "jarKeys": [],
      "group": "default"
    }
    ```

## list all pipelines

*GET /v0/pipelines*

Listing all pipelines is a expensive operation as it invokes a iteration
to all objects stored in pipeline. The loop will do a lot of checks and
fetch status, metrics and log from backend clusters. If you have the
name of pipeline, please use [GET]({{< relref "#get" >}}) to fetch
details of **single** pipeline.

the accepted query keys are listed below.

1. group
2. name
3. jarKeys
4. lastModified
5. tags

* Example Response
    ```json
    [
      {
        "name": "pipeline0",
        "lastModified": 1578641282237,
        "endpoints": [
          {
            "group": "default",
            "name": "topic1",
            "kind": "topic"
          }
        ],
        "tags": {},
        "objects": [
          {
            "name": "topic1",
            "state": "RUNNING",
            "lastModified": 1578641231579,
            "tags": {},
            "nodeMetrics": [],
            "kind": "topic",
              "group": "default"
            }
        ],
        "jarKeys": [],
        "group": "default"
      }
    ]
    ```

*GET /v0/pipelines?name=${pipelineName}*

* Example Response
    ```json
    [
      {
        "name": "pipeline0",
        "lastModified": 1578647223700,
        "endpoints": [
          {
            "group": "default",
            "name": "topic1",
            "kind": "topic"
          }
        ],
        "tags": {},
        "objects": [],
        "jarKeys": [],
        "group": "default"
      }
    ]
    ```

## delete a pipeline

*DELETE /v0/pipelines/$name*

Deleting a pipeline does not delete the objects related to the pipeline.

* Example Response
    ```
    204 NoContent
    ```

{{% alert hint %}}
It is ok to delete a nonexistent pipeline, and the response is
204 NoContent. However, it is illegal to remove a pipeline having
any running objects
{{% /alert %}}

## get a pipeline {#get}

*GET /v0/pipelines/$name*

* Example Response
    ```json
    {
      "name": "pipeline0",
      "lastModified": 1578647223700,
      "endpoints": [
        {
          "group": "default",
          "name": "topic1",
          "kind": "topic"
        }
      ],
      "tags": {},
      "objects": [],
      "jarKeys": [],
      "group": "default"
    }
    ```

{{% alert hint %}}
The field "objects" displays only existent endpoints.
{{% /alert %}}

## refresh a pipeline

*PUT /v0/pipelines/$name/refresh*

Requires Ohara Configurator to cleanup nonexistent objects of pipeline.
Pipeline is a group of objects and it contains, sometimes, some
nonexistent objects. Those nonexistent objects won't hurt our services
but it may be ugly and weird to read. Hence, the (helper) API do a
background cleanup for your pipeline. The cleanup rules are shown below.

1. the endpoint having nonexistent "from" is removed
2. the objects in "to" get removed

* Example Response
    ```http request
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get pipeline]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}
