---
title: Topic
linktitle: Topic API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  011x:
    parent: REST APIs
    weight: 170
---

Ohara topic is based on kafka topic. It means the creation of topic on
ohara will invoke a creation of kafka also. Also, the delete to Ohara
topic also invoke a delete request to kafka. The common properties in
topic are shown below.

1. group (**string**) - topic group. The legal character is number,
   lowercase alphanumeric characters, or '.'
2. name (**string**) - topic name. The legal character is number,
   lowercase alphanumeric characters, or '.'
3. brokerClusterKey (**Object**) - target broker cluster.
   - brokerClusterKey.group (**option(string)**) - the group of
     cluster
   - brokerClusterKey.name (**string**) - the name of cluster
  {{% alert note %}}
  the following forms are legal as well: (1) `{"name": "n"}`, (2) `"n"`.
  Both forms are converted to `{"group": "default", "name": "n"}`
  {{% /alert %}}
4. numberOfReplications (**option(int)**) - the number of
   replications
5. numberOfPartitions (**option(int)**)- the number of partitions for
   this topic
6. tags (**option(object)**) - the extra description to this object

  {{% alert note %}}
  1. The name must be unique in a broker cluster.
  2. There are many other available configs which are useful in
     creating topic. Please ref [broker clusters]({{< relref "./brokers.md" >}})
     to see how to retrieve the available configs for specific broker
     cluster.
  {{% /alert %}}

The following information are tagged by Ohara.

1. state (**option(string)**) - state of a running topic. nothing if
   the topic is not running.
2. partitionInfos (**Array(object)**) - the details of partitions.
   - index (**int**) - the index of partition
   - leaderNode (**String**) - the leader (node) of this partition
   - replicaNodes (**Array(String)**) - the nodes hosting the
     replica for this partition
   - inSyncReplicaNodes (**Array(String)**) - the nodes which have
     fetched the newest data from leader
   - beginningOffset (**long**) - the beginning offset
   - endOffset (**endOffset**) - the latest offset (Normally, it is
     the latest commit data)
3. group (**string**) - the group value is always "default"
4. [nodeMetrics]({{< relref "#metrics" >}}) (**object**) - 
   the metrics number of a running topic
5. lastModified (**long**) - the last time to update this ftp
   information

## store a topic properties

*POST /v0/topics*

{{% alert note %}}
1. the name you pass to Ohara is used to build topic on kafka, and it
   is restricted by Kafka ([a-zA-Z0-9.\_-]) 
1. the ignored fields will
   be auto-completed by Ohara Configurator. Also, you could update/replace
   it by UPDATE request later. 
1. this API does NOT create a topic on
   broker cluster. Instead, you should send START request to run a topic on
   broker cluster actually 
1. There are many other available configs which
   are useful in creating topic. Please ref
   [broker clusters]({{< relref "./brokers.md" >}}) to see
   how to retrieve the available configs for specific broker cluster.
{{% /alert %}}

* Example Request
    ```json
    {
      "brokerClusterKey": "bk",
      "name": "topic0",
      "numberOfReplications": 1,
      "numberOfPartitions": 1
    }
    ```

* Example Response
    ```json
    {
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "topic0",
      "partitionInfos": [],
      "lastModified": 1578537142950,
      "tags": {},
      "numberOfReplications": 1,
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "BytesInPerSec",
              "name": "BytesInPerSec",
              "queryTime": 1585069111069,
              "unit": "bytes / SECONDS",
              "value": 2143210885
            },
            {
              "document": "MessagesInPerSec",
              "name": "MessagesInPerSec",
              "queryTime": 1585069111069,
              "unit": "messages / SECONDS",
              "value": 2810000.0
            },
            {
              "document": "TotalProduceRequestsPerSec",
              "name": "TotalProduceRequestsPerSec",
              "queryTime": 1585069111069,
              "unit": "requests / SECONDS",
              "value": 137416.0
            }
          ]
        }
      },
      "group":"default",
      "numberOfPartitions": 1
    }
    ```

{{% alert hint %}}
The topic, which is just created, does not have any metrics.
{{% /alert %}}

## update a topic properties

*PUT /v0/topics/${name}?group=${group}*

* Example Request
    ```json
    {
      "numberOfPartitions": 3
    }
    ```

* Example Response
    ```json
    {
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "topic0",
      "partitionInfos": [],
      "lastModified": 1578537915735,
      "tags": {},
      "numberOfReplications": 1,
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "BytesInPerSec",
              "name": "BytesInPerSec",
              "queryTime": 1585069111069,
              "unit": "bytes / SECONDS",
              "value": 2143210885
            },
            {
              "document": "MessagesInPerSec",
              "name": "MessagesInPerSec",
              "queryTime": 1585069111069,
              "unit": "messages / SECONDS",
              "value": 2810000.0
            },
            {
              "document": "TotalProduceRequestsPerSec",
              "name": "TotalProduceRequestsPerSec",
              "queryTime": 1585069111069,
              "unit": "requests / SECONDS",
              "value": 137416.0
            }
          ]
        }
      },
      "group": "default",
      "numberOfPartitions": 3
    }
    ```

## list all topics properties

*GET /v0/topics?${key}=${value}*

the accepted query keys are listed below. 
- group 
- name 
- state
- lastModified 
- tags 
- tag: this field is similar to tags but it addresses the "contain" behavior. 
- key

{{% alert hint %}}
Using "NONE" represents the nonexistence of state.
{{% /alert %}}

* Example Response
    ```json
    [
      {
        "brokerClusterKey": {
          "group": "default",
          "name": "bk"
        },
        "name": "topic1",
        "partitionInfos": [],
        "lastModified": 1578537915735,
        "tags": {},
        "numberOfReplications": 1,
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "BytesInPerSec",
              "name": "BytesInPerSec",
              "queryTime": 1585069111069,
              "unit": "bytes / SECONDS",
              "value": 2143210885
            },
            {
              "document": "MessagesInPerSec",
              "name": "MessagesInPerSec",
              "queryTime": 1585069111069,
              "unit": "messages / SECONDS",
              "value": 2810000.0
            },
            {
              "document": "TotalProduceRequestsPerSec",
              "name": "TotalProduceRequestsPerSec",
              "queryTime": 1585069111069,
              "unit": "requests / SECONDS",
              "value": 137416.0
            }
          ]
        }
      },
        "group": "default",
        "numberOfPartitions": 3
      }
    ]
    ```

## delete a topic properties

*DELETE /v0/topics/${name}?group=${group}*

* Example Response
    
    ````
    204 NoContent
    ````
    

{{% alert hint %}}
It is ok to delete a nonexistent topic, and the response is **204 NoContent**. 
You must be stopped the delete topic.
{{% /alert %}}

## get a topic properties {#get}

*GET /v0/topics/${name}*

* Example Response
    ```json
    {
      "brokerClusterKey": {
        "group": "default",
        "name": "bk"
      },
      "name": "topic1",
      "partitionInfos": [],
      "lastModified": 1578537915735,
      "tags": {},
      "numberOfReplications": 1,
      "nodeMetrics": {
        "node00": {
          "meters": [
            {
              "document": "BytesInPerSec",
              "name": "BytesInPerSec",
              "queryTime": 1585069111069,
              "unit": "bytes / SECONDS",
              "value": 2143210885
            },
            {
              "document": "MessagesInPerSec",
              "name": "MessagesInPerSec",
              "queryTime": 1585069111069,
              "unit": "messages / SECONDS",
              "value": 2810000.0
            },
            {
              "document": "TotalProduceRequestsPerSec",
              "name": "TotalProduceRequestsPerSec",
              "queryTime": 1585069111069,
              "unit": "requests / SECONDS",
              "value": 137416.0
            }
          ]
        }
      },
      "group": "default",
      "numberOfPartitions": 3
    }
    ```

## start a topic on remote broker cluster

*PUT /v0/topics/${name}/start*

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get Topic info]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}

## stop a topic from remote broker cluster

*PUT /v0/topics/${name}/stop*

{{% alert hint %}}
the topic will lose all data after stopping.
{{% /alert %}}

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get Topic info]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}
