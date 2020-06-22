---
title: Inspect
linktitle: Inspect
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 100
oharaVersion: 0.10.0
oharaBranch: 0.10.0
---

Inspect APIs is a powerful tool that it enable you to "see" what in
the target via Configurator. For example, you can get the definitions
from specific image, or you can see what connectors or stream app in the
specific file.

## get Ohara Configurator info

*GET /v0/inspect/configurator*

the format of response of Ohara Configurator is shown below.

1. versionInfo (**object**) --- version details of Ohara Configurator
   - branch (**string**) --- the branch name of Ohara Configurator
   - version (**string**) --- the release version of Ohara
     Configurator
   - revision (**string**) --- commit hash of Ohara Configurator. You
     can trace the hash code via
     [Github](https://github.com/oharastream/ohara/commits/master)
   - user (**string**) --- the release manager of Ohara Configurator.
   - date (**string**) --- the date of releasing Ohara Configurator.
2. mode (**string**) --- the mode of this configurator. There are three
   modes now:
   - K8S: k8s mode is for the production.
   - SSH: ssh is useful to simple env.
   - FAKE: fake mode is used to test APIs.

* Example Response
    ```json
    {
      "versionInfo": {
        "branch": "{{< param oharaBranch >}}",
        "revision": "b303f3c2e52647ee5e79e55f9d74a5e51238a92c",
        "version": "{{< param oharaVersion >}}",
        "date": "2020-01-08 06:05:47",
        "user": "root"
      },
      "mode": "K8S"
    }
    ```

## get zookeeper/broker/worker/stream info

*GET /v0/inspect/$service*

This API used to fetch the definitions for specific cluster service. The
following fields are returned.

1. imageName (**string**) --- the image name of service
2. settingDefinitions (**array(object)**) --- the available settings
   for this service (see [setting]({{< relref "../setting_definition.md" >}}))

the available variables for $service are shown below.

1. zookeeper
2. broker
3. worker
4. stream

* Example Response
    ```json
    {
      "imageName": "oharastream/zookeeper:{{< param oharaVersion >}}",
      "settingDefinitions": [
        {
           "blacklist": [],
           "reference": "NONE",
           "displayName": "peerPort",
           "regex": null,
           "internal": false,
           "permission": "EDITABLE",
           "documentation": "the port exposed to each quorum",
           "necessary": "RANDOM_DEFAULT",
           "valueType": "BINDING_PORT",
           "tableKeys": [],
           "orderInGroup": 10,
           "key": "peerPort",
           "defaultValue": null,
           "recommendedValues": [],
           "group": "core"
        },
      ],
      "classInfos": []
    }
    ```

## get running zookeeper/broker/worker/stream info

*GET /v0/inspect/$service/$name?group=$group*

This API used to fetch the definitions for specific cluster service and
the definitions of available classes in the service. The following
fields are returned.

1. imageName (**string**) --- the image name of service
2. settingDefinitions (**array(object)**) --- the available settings
   for this service (see [setting]({{< relref "../setting_definition.md" >}}))
3. classInfos (**array(object)**) --- the information available classes
   in this service
   - classInfos[i].className --- the name of this class
   - classInfos[i].classType --- the type of this class. for example,
     topic, source connector, sink connector or stream app
   - classInfos[i].settingDefinitions --- the definitions of this
     class

the available variables for $service are shown below.

1. zookeeper
2. broker
3. worker
4. stream

* Example Response
    ```json
    {
      "imageName": "oharastream/broker:{{< param oharaVersion >}}",
      "settingDefinitions": [
        {
          "blacklist": [],
          "reference": "NONE",
          "displayName": "xmx",
          "regex": null,
          "internal": false,
          "permission": "EDITABLE",
          "documentation": "maximum memory allocation (in MB)",
          "necessary": "OPTIONAL_WITH_DEFAULT",
          "valueType": "POSITIVE_LONG",
          "tableKeys": [],
          "orderInGroup": 8,
          "key": "xmx",
          "defaultValue": 1024,
          "recommendedValues": [],
          "group": "core"
        }
      ],
      "classInfos": [
        {
          "classType": "topic",
          "className": "N/A",
          "settingDefinitions": [
            {
              "blacklist": [],
              "reference": "NONE",
              "displayName": "numberOfPartitions",
              "regex": null,
              "internal": false,
              "permission": "EDITABLE",
              "documentation": "the number of partitions",
              "necessary": "OPTIONAL_WITH_DEFAULT",
              "valueType": "POSITIVE_INT",
              "tableKeys": [],
              "orderInGroup": 4,
              "key": "numberOfPartitions",
              "defaultValue": 1,
              "recommendedValues": [],
              "group": "core"
            }
          ]
        }
      ]
    }
    ```

## Query Database

*POST /v0/inspect/rdb*

This API returns the table details of a relational database. This API
invokes a running connector on worker cluster to fetch database
information and return to Ohara Configurator. You should deploy suitable
jdbc driver on worker cluster before using this API. Otherwise, you will
get a exception returned by Ohara Configurator. The query consists of
following fields.

1. url (**string**) --- jdbc url
2. user (**string**) --- user who can access target database
3. password (**string**) --- password which can access target database
4. workerClusterKey (**Object**) --- target worker cluster.
   - workerClusterKey.group (**option(string)**) --- the group of
     cluster
   - workerClusterKey.name (**string**) --- the name of cluster

{{% alert note %}}
the following forms are legal as well: (1) `{"name": "n"}`, (2) `"n"`.
Both forms are converted to `{"group": "default", "name": "n"}`
{{% /alert %}}

5. catalogPattern (**option(string)**) --- filter returned tables
   according to catalog
6. schemaPattern (**option(string)**) --- filter returned tables
   according to schema
7. tableName (**option(string)**) --- filter returned tables according
   to name

* Example Request
    ```json
    {
      "url": "jdbc:postgresql://localhost:5432/postgres",
      "user": "ohara",
      "password": "123456",
      "workerClusterKey": "wk00"
    }
    ```

* Example Response
  1. name (**string**) --- database name
  2. tables (**array(object)**)
     - tables[i].catalogPattern (**option(object)**) --- table's
       catalog pattern
     - tables[i].schemaPattern (**option(object)**) --- table's
       schema pattern
     - tables[i].name (**option(object)**) --- table's name
     - tables[i].columns (**array(object)**) --- table's columns
       - tables[i].columns[j].name (**string**) --- column's
         columns
       - tables[i].columns[j].dataType (**string**) ---
         column's data type
       - tables[i].columns[j].pk (**boolean**) --- true if
         this column is pk. otherwise false
         
    ```json
    {
      "name": "postgresql",
      "tables": [
        {
          "schemaPattern": "public",
          "name": "table1",
          "columns": [
            {
              "name": "column1",
              "dataType": "timestamp",
              "pk": false
            },
            {
              "name": "column2",
              "dataType": "varchar",
              "pk": true
            }
          ]
        }
      ]
    }
    ```

## Query Topic

*POST /v0/inspect/topic/$name?group=$group&timeout=$timeout&limit=$limit*

Fetch the latest data from a topic. the query arguments are shown below.

1. timeout (**long**) --- break the fetch if this timeout is reached
2. limit (**long**) --- the number of messages in topic

the response includes following items.

1. messages (**Array(Object)**) --- messages
  - messages[i].partition (**int**) --- the index of partition
  - messages[i].offset (**Long**) --- the offset of this message
  - messages[i].sourceClass (**Option(String)**) --- class name of
    the component which generate this data
  - messages[i].sourceKey (**Option(Object)**) --- object key of the
    component which generate this data
  - messages[i].value (**Option(Object)**) --- the value of this
    message
  - messages[i].error (**Option(String)**) --- error message happen
    in failing to parse value

* Example Response
    ```json
    {
      "messages": [
        {
          "sourceKey": {
            "group": "default",
            "name": "perf"
          },
          "sourceClass": "oharastream.ohara.connector.perf.PerfSourceTask",
          "partition": 0,
          "offset": 0,
          "value": {
            "a": "c54e2f3477",
            "b": "32ae422fb5",
            "c": "53e448ab80",
            "tags": []
          }
        }
      ]
    }
    ```

## Query File

1. name (**string**) --- the file name without extension
2. group (**string**) --- the group name (we use this field to separate
   different workspaces)
3. size (**long**) --- file size
4. tags (**object**) --- the extra description to this object
5. lastModified (**long**) --- the time of uploading this file
6. classInfos (**array(object)**) --- the information of available
   classes in this file
  - classInfos[i].className --- the name of this class
  - classInfos[i].classType --- the type of this class. for example,
    topic, source connector, sink connector or stream app
  - classInfos[i].settingDefinitions --- the definitions of this
    class

*POST /v0/inspect/files*

* Example Request
    ```http request
    Content-Type: multipart/form-data
    file="ohara-it-sink.jar"
    group="default"
    ```

* Example Response
    ```json
    {
      "name": "ohara-it-sink.jar",
      "size": 7902,
      "lastModified": 1579055900202,
      "tags": {},
      "classInfos": [
        {
          "classType": "sink",
          "className": "oharastream.ohara.it.connector.IncludeAllTypesSinkConnector",
          "settingDefinitions": [
            {
              "blacklist": [],
              "reference": "NONE",
              "displayName": "kind",
              "regex": null,
              "internal": false,
              "permission": "READ_ONLY",
              "documentation": "kind of connector",
              "necessary": "OPTIONAL_WITH_DEFAULT",
              "valueType": "STRING",
              "tableKeys": [],
              "orderInGroup": 13,
              "key": "kind",
              "defaultValue": "sink",
              "recommendedValues": [],
              "group": "core"
            }
          ]
        }
      ],
      "group": "default"
    }
    ```
