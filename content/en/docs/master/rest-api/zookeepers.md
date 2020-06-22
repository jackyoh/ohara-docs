---
title: Zookeeper
linktitle: Zookeeper
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 200
oharaVersion: 0.10.0    
---

[Zookeeper](https://zookeeper.apache.org) service is the base of all
other services. It is also the fist service you should set up. At the
beginning, you can deploy zookeeper cluster in single node. However, it
may be unstable since single node can't guarantee the data durability
when node crash. In production you should set up zookeeper cluster on 3
nodes at least.

Zookeeper service has many configs which make you spend a lot of time to
read and set. Ohara provides default values to all configs but open a
room to enable you to overwrite somethings you do care.

The properties which can be set by user are shown below.

1. name (**string**) - cluster name. The legal character is number,
   lowercase alphanumeric characters, or '.'
2. group (**string**) - cluster group. The legal character is number,
   lowercase alphanumeric characters, or '.'
3. imageName (**string**) - docker image
4. jmxPort (**int**) - zookeeper jmx port
5. clientPort (**int**) - zookeeper client port
6. electionPort (**int**) - used to select the zk node leader
7. peerPort (**int**) - port used by internal communication
8. nodeNames (**array(string)**) - the nodes running the zookeeper
   process
9. tags (**object**) - the user defined parameters

The following information are updated by Ohara.

1. aliveNodes (**array(string)**) - the nodes that host the running
   containers of zookeeper
2. state (**option(string)**) - only started/failed zookeeper has
   state (RUNNING or DEAD)
3. error (**option(string)**) - the error message from a failed
   zookeeper. If zookeeper is fine or un-started, you won't get this
   field.
4. lastModified (**long**) - last modified this jar time

## create a zookeeper properties {#create-properties}

*POST /v0/zookeepers*

* Example Request
    ```json
    {
      "name": "zk",
      "nodeNames": [
        "node00"
      ]
    }
    ```

* Example Response
    ```json
    {
      "syncLimit": 5,
      "name": "zk00",
      "xms": 1024,
      "routes": {},
      "lastModified": 1578642569693,
      "dataDir": "/home/ohara/default/data",
      "tags": {},
      "electionPort": 44371,
      "xmx": 1024,
      "imageName": "oharastream/zookeeper:{{< param oharaVersion >}}",
      "aliveNodes": [],
      "initLimit": 10,
      "jmxPort": 33915,
      "clientPort": 42006,
      "peerPort": 46559,
      "tickTime": 2000,
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

{{% alert hint %}}
All ports have default value so you can ignore them when creating
zookeeper cluster. However, the port conflict detect does not allow
you to reuse port on different purpose (a dangerous behavior, right?).
{{% /alert %}}

## list all zookeeper clusters

*GET /v0/zookeepers*

the accepted query keys are listed below.

1. group
2. name
3. lastModified
4. tags
5. tag - this field is similar to tags but it addresses the "contain"
   behavior.
6. state
7. aliveNodes
8. key

* Example Response
    ```json
    [
      {
        "syncLimit": 5,
        "name": "zk00",
        "xms": 1024,
        "routes": {},
        "lastModified": 1578642569693,
        "dataDir": "/home/ohara/default/data",
        "tags": {},
        "electionPort": 44371,
        "xmx": 1024,
        "imageName": "oharastream/zookeeper:{{< param oharaVersion >}}",
        "aliveNodes": [],
        "initLimit": 10,
        "jmxPort": 33915,
        "clientPort": 42006,
        "peerPort": 46559,
        "tickTime": 2000,
        "group": "default",
        "nodeNames": [
          "node00"
        ]
      }
    ]
    ```

## update zookeeper cluster properties

*PUT /v0/zookeepers/$name?group=$group*

{{% alert hint %}}
If the required zookeeper (group, name) was not exists, we will try to
use this request as POST
{{% /alert %}}

* Example Request
    ```json
    {
      "jmxPort": 12345
    }
    ```

* Example Response
    ```json
    {
      "syncLimit": 5,
      "name": "zk00",
      "xms": 1024,
      "routes": {},
      "lastModified": 1578642751122,
      "dataDir": "/home/ohara/default/data",
      "tags": {},
      "electionPort": 44371,
      "xmx": 1024,
      "imageName": "oharastream/zookeeper:{{< param oharaVersion >}}",
      "aliveNodes": [],
      "initLimit": 10,
      "jmxPort": 12345,
      "clientPort": 42006,
      "peerPort": 46559,
      "tickTime": 2000,
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## delete a zookeeper properties

*DELETE /v0/zookeepers/$name?group=$group*

You cannot delete properties of an non-stopped zookeeper cluster. We
will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    
    ````
    204 NoContent
    ````
    

{{% alert hint %}}
It is ok to delete an nonexistent zookeeper cluster, and the
response is 204 NoContent.
{{% /alert %}}

## get a zookeeper cluster {#get}

*GET /v0/zookeepers/$name?group=$group*

Get zookeeper information by name and group. This API could fetch all
information of a zookeeper (include state). We will use the default
value as the query parameter "?group=" if you don't specify it.

* Example Response
    ```json
    {
      "syncLimit": 5,
      "name": "zk00",
      "xms": 1024,
      "routes": {},
      "lastModified": 1578642569693,
      "dataDir": "/home/ohara/default/data",
      "tags": {},
      "electionPort": 44371,
      "xmx": 1024,
      "imageName": "oharastream/zookeeper:{{< param oharaVersion >}}",
      "aliveNodes": [],
      "initLimit": 10,
      "jmxPort": 33915,
      "clientPort": 42006,
      "peerPort": 46559,
      "tickTime": 2000,
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## start a zookeeper cluster

*PUT /v0/zookeepers/$name/start?group=$group*

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get zookeeper cluster]({{< relref "#get" >}}) to fetch 
up-to-date status
{{% /alert %}}

## stop a zookeeper cluster

Gracefully stopping a running zookeeper cluster. It is disallowed to
stop a zookeeper cluster used by a running
[broker cluster]({{< relref "brokers.md" >}}).

*PUT /v0/zookeepers/$name/stop?group=$group[&force=true]*

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Query Parameters
  1. force (**boolean**) - true if you don't want to wait the
     graceful shutdown (it can save your time but may damage your
     data).

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get zookeeper cluster]({{< relref "#get" >}}) 
to fetch up-to-date status
{{% /alert %}}

## delete a node from a running zookeeper cluster

Unfortunately, it is a litter dangerous to remove a node from a running
zookeeper cluster so we don't support it yet.

## add a node to a running zookeeper cluster

Unfortunately, it is a litter hard to add a node to a running zookeeper
cluster so we don't support it yet.
