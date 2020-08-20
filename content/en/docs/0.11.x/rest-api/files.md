---
title: Files
linktitle: Files API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  011x:
    parent: REST APIs
    weight: 90
---

Ohara encourages user to write custom application if the official
applications can satisfy requirements for your use case. Jar APIs is a
useful entry of putting your jar on Ohara and then start related
services with it. For example,
[Worker APIs]({{< relref "./workers.md#rest-workers-create" >}}) accept
a **sharedJarKeys** element which can carry the jar name pointing to an
existent jar in Ohara. The worker cluster will load all connectors of
the input jar, and then you are able to use the connectors on the worker
cluster.

The File API upload jar file to use by the
[Worker]({{< relref "./workers.md" >}}) and [Stream]({{< relref "./streams.md" >}}).

{{% alert hint %}}
The file used by a worker or stream can't be either updated or deleted.
{{% /alert %}}

The properties stored by Ohara are shown below.

1. name (**string**) --- the file name without extension. The legal
   character is number, lowercase alphanumeric characters, or '.'
2. group (**string**) --- the group name (we use this field to separate
   different workspaces). The legal character is number, lowercase
   alphanumeric characters, or '.'
3. size (**long**) --- file size
4. url (**option(string)**) --- url to download this jar from Ohara
   Configurator. Noted not all jars are downloadable to user.
5. lastModified (**long**) --- the time of uploading this file
6. tags (**object**) --- the user defined parameters
7. bytes (**array(object)**) --- read file content to bytes
8. classInfos (**array(object)**) --- the information of available
   classes in this file
   - classInfos[i].className --- the name of this class
   - classInfos[i].classType --- the type of this class. for example,
     topic, source connector, sink connector or stream app
   - classInfos[i].settingDefinitions --- the definitions of this
     class  

{{% alert hint %}}
The field "classInfos" is empty if the file is NOT a valid jar.
{{% /alert %}}

## upload a file to Ohara

Upload a file to Ohara with field name : "jar" and group name : "group"
the text field "group" could be empty and we will generate a random
string.

*POST /v0/files*

* Example Request
    ```http request
    Content-Type: multipart/form-data
    file="ohara-it-stream.jar"
    group="default"
    tags={}
    ```

{{% alert hint %}}
You have to specify the file name since it is a part of metadata
stored by Ohara. Noted, the later uploaded file can overwrite the
older one
{{% /alert %}}

* Example Response

    ```json
    {
      "name": "ohara-it-stream.jar",
      "size": 1896,
      "url": "http://localhost:12345/v0/downloadFiles/default/ohara-it-stream.jar",
      "lastModified": 1578967196525,
      "tags": {},
      "classInfos": [
        {
          "classType": "stream",
          "className": "oharastream.ohara.it.stream.DumbStream",
          "settingDefinitions": [
            {
              "blacklist": [],
              "reference": "BROKER_CLUSTER",
              "displayName": "Broker cluster key",
              "regex": null,
              "internal": false,
              "permission": "EDITABLE",
              "documentation": "the key of broker cluster used to transfer data for this stream",
              "necessary": "REQUIRED",
              "valueType": "OBJECT_KEY",
              "tableKeys": [],
              "orderInGroup": 0,
              "key": "brokerClusterKey",
              "defaultValue": null,
              "recommendedValues": [],
              "group": "core"
            }
          ]
        }
      ],
      "group": "default"
    }
    ```

## list all jars

Get all jars from specific group of query parameter. If no query
parameter, wll return all jars.

*GET /v0/files?group=default*

* Example Response
    ```json
    [
      {
        "name": "ohara-it-stream.jar",
        "size": 1896,
        "url": "http://localhost:5000/v0/downloadFiles/default/ohara-it-stream.jar",
        "lastModified": 1578973197877,
        "tags": {},
        "classInfos": [
          {
            "classType": "stream",
            "className": "oharastream.ohara.it.stream.DumbStream",
            "settingDefinitions": [
              {
                "blacklist": [],
                "reference": "BROKER_CLUSTER",
                "displayName": "Broker cluster key",
                "regex": null,
                "internal": false,
                "permission": "EDITABLE",
                "documentation": "the key of broker cluster used to transfer data for this stream",
                "necessary": "REQUIRED",
                "valueType": "OBJECT_KEY",
                "tableKeys": [],
                "orderInGroup": 0,
                "key": "brokerClusterKey",
                "defaultValue": null,
                "recommendedValues": [],
                "group": "core"
              },
            ]
          }
        ],
        "group": "default"
      }
    ]
    ```

## delete a file

Delete a file with specific name and group. Note: the query parameter
must exist.

*DELETE /v0/files/$name?group=default*

* Example Response
    ````
    204 NoContent
    ````

{{% alert hint %}}
It is ok to delete a nonexistent jar, and the response is 204
NoContent. If you delete a file is used by other services, you also
break the scalability of service as you can't run the jar on any new
nodes
{{% /alert %}}

## get a file

Get a file with specific name and group. Note: the query parameter must
exists.

*GET /v0/files/$name?group=default*

* Example Response
    ```json
    {
      "name": "ohara-it-stream.jar",
      "size": 1896,
      "url": "http://localhost:5000/v0/downloadFiles/default/ohara-it-stream.jar",
      "lastModified": 1578973197877,
      "tags": {},
      "classInfos": [
        {
          "classType": "stream",
          "className": "oharastream.ohara.it.stream.DumbStream",
          "settingDefinitions": [
            {
              "blacklist": [],
              "reference": "BROKER_CLUSTER",
              "displayName": "Broker cluster key",
              "regex": null,
              "internal": false,
              "permission": "EDITABLE",
              "documentation": "the key of broker cluster used to transfer data for this stream",
              "necessary": "REQUIRED",
              "valueType": "OBJECT_KEY",
              "tableKeys": [],
              "orderInGroup": 0,
              "key": "brokerClusterKey",
              "defaultValue": null,
              "recommendedValues": [],
              "group": "core"
            }
          ]
        }
      ],
      "group": "default"
    }
    ```

## update tags of file

*PUT /v0/files/$name?group=default*

* Example Response
    ```json
    {
      "tags": {
        "a": "b"
      }
    }
    ```

{{% alert hint %}}
it returns error code if input group/name are not associated to an
existent file.
{{% /alert %}}

* Example Response
    ```json
    {
      "name": "ohara-it-stream.jar",
      "size": 1896,
      "url": "http://localhost:5000/v0/downloadFiles/default/ohara-it-stream.jar",
      "lastModified": 1578974415307,
      "tags": {
        "a": "b"
      },
      "classInfos": [
        {
          "classType": "stream",
          "className": "oharastream.ohara.it.stream.DumbStream",
          "settingDefinitions": [
            {
              "blacklist": [],
              "reference": "BROKER_CLUSTER",
              "displayName": "Broker cluster key",
              "regex": null,
              "internal": false,
              "permission": "EDITABLE",
              "documentation": "the key of broker cluster used to transfer data for this stream",
              "necessary": "REQUIRED",
              "valueType": "OBJECT_KEY",
              "tableKeys": [],
              "orderInGroup": 0,
              "key": "brokerClusterKey",
              "defaultValue": null,
              "recommendedValues": [],
              "group": "core"
            }
          ]
        }
      ],
      "group": "default"
    }
    ```
