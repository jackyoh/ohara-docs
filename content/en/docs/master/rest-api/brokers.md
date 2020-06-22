---
title: Broker
linktitle: Broker
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 60
oharaVersion: 0.10.0
---

[Broker](https://kafka.apache.org/intro) is core of data transmission in
ohara. The topic, which is a part our data lake, is hosted by broker
cluster. The number of brokers impacts the performance of transferring
data and data durability. But it is ok to setup broker cluster in single
node when testing. As with [zookeeper]({{< relref "zookeepers.md" >}}), broker has
many configs also. Ohara still provide default to most configs and then
enable user to overwrite them.

Broker is based on [zookeeper]({{< relref "zookeepers.md" >}}), 
hence you have to create zookeeper cluster first. Noted
that a zookeeper cluster can be used by only a broker cluster. It will
fail if you try to multi broker cluster on same zookeeper cluster.

The properties which can be set by user are shown below.

1. name (**string**) --- cluster name
2. group (**string**) --- cluster group
3. imageName (**string**) --- docker image
4. clientPort (**int**) --- broker client port.
5. jmxPort (**int**) --- port used by jmx service
6. zookeeperClusterKey (**object**) --- key of zookeeper cluster used
   to store metadata of broker cluster
   - zookeeperClusterKey.group(**option(string)**) --- the group of
     zookeeper cluster
   - zookeeperClusterKey.name(**string**) --- the name of zookeeper
     cluster
     
{{% alert note %}}
the following forms are legal as well: (1) `{"name": "n"}`, (2) `"n"`.
Both forms are converted to `{"group": "default", "name": "n"}`
{{% /alert %}}

7. nodeNames (**array(string)**) --- the nodes running the zookeeper
   process
8. tags (**object**) --- the user defined parameters

The following information are updated by Ohara.

1. aliveNodes (**array(string)**) --- the nodes that host the running
   containers of broker cluster
2. state (**option(string)**) --- only started/failed broker has state
   (RUNNING or DEAD)
3. error (**option(string)**) --- the error message from a failed
   broker. If broker is fine or un-started, you won't get this field.
4. lastModified (**long**) --- last modified this jar time

## create a broker cluster {#create}

*POST /v0/brokers*

* Example Request
    ```json
    {
      "name": "bk",
      "nodeNames": ["node00"],
      "zookeeperClusterKey": "zk"
    }
    ```

* Example Response
    ```json
    {
      "name": "bk00",
      "offsets.topic.replication.factor": 1,
      "xms": 1024,
      "routes": {},
      "num.partitions": 1,
      "lastModified": 1578903360246,
      "num.network.threads": 1,
      "tags": {},
      "xmx": 1024,
      "imageName": "oharastream/broker:{{< param oharaVersion >}}",
      "log.dirs": "/home/ohara/default/data",
      "aliveNodes": [],
      "jmxPort": 42020,
      "num.io.threads": 1,
      "clientPort": 39614,
      "zookeeperClusterKey": {
        "group": "default",
        "name": "zk00"
      },
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## list all broker clusters

*GET /v0/brokers*

* Example Response
    ```json
    [
      {
        "name": "bk00",
        "offsets.topic.replication.factor": 1,
        "xms": 1024,
        "routes": {},
        "num.partitions": 1,
        "lastModified": 1578903360246,
        "num.network.threads": 1,
        "tags": {},
        "xmx": 1024,
        "imageName": "oharastream/broker:{{< param oharaVersion >}}",
        "log.dirs": "/home/ohara/default/data",
        "aliveNodes": [],
        "jmxPort": 42020,
        "num.io.threads": 1,
        "clientPort": 39614,
        "zookeeperClusterKey": {
          "group": "default",
          "name": "zk00"
        },
        "group": "default",
        "nodeNames": [
          "node00"
        ]
      }
    ]
    ```

## update broker cluster properties

*PUT /v0/brokers/$name?group=$group*

{{% alert hint %}}
If the required broker (group, name) was not exists, we will try to use
this request as POST
```json
{
  "xmx": 2048
}
```
{{% /alert %}}

* Example Response
    ```json
    {
      "name": "bk00",
      "offsets.topic.replication.factor": 1,
      "xms": 1024,
      "routes": {},
      "num.partitions": 1,
      "lastModified": 1578903494681,
      "num.network.threads": 1,
      "tags": {},
      "xmx": 2048,
      "imageName": "oharastream/broker:{{< param oharaVersion >}}",
      "log.dirs": "/home/ohara/default/data",
      "aliveNodes": [],
      "jmxPort": 42020,
      "num.io.threads": 1,
      "clientPort": 39614,
      "zookeeperClusterKey": {
        "group": "default",
        "name": "zk00"
      },
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## delete a broker properties

*DELETE /v0/brokers/$name?group=$group*

You cannot delete properties of an non-stopped broker cluster. We will
use the default value as the query parameter "?group=" if you don't
specify it.

* Example Response
    ```
    204 NoContent
    ```
    

{{% alert hint %}}
It is ok to delete an nonexistent broker cluster, and the response
is 204 NoContent.
{{% /alert %}}

## get a broker cluster {#get}

*GET /v0/brokers/$name?group=$group*

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    ```json
    {
      "name": "bk00",
      "offsets.topic.replication.factor": 1,
      "xms": 1024,
      "routes": {},
      "num.partitions": 1,
      "lastModified": 1578903494681,
      "num.network.threads": 1,
      "tags": {},
      "xmx": 2048,
      "imageName": "oharastream/broker:{{< param oharaVersion >}}",
      "log.dirs": "/home/ohara/default/data",
      "aliveNodes": [],
      "jmxPort": 42020,
      "num.io.threads": 1,
      "clientPort": 39614,
      "zookeeperClusterKey": {
        "group": "default",
        "name": "zk00"
      },
      "group": "default",
      "nodeNames": [
        "node00"
      ]
    }
    ```

## start a broker cluster

*PUT /v0/brokers/$name/start?group=$group*

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    ```http request
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get broker cluster]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}

## stop a broker cluster

Gracefully stopping a running broker cluster. It is disallowed to stop a
broker cluster used by a running
[worker cluster]({{< relref "workers.md" >}}).

*PUT /v0/brokers/$name/stop?group=$group[&force=true]*

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Query Parameters
  - force (**boolean**) --- true if you don't want to wait the
    graceful shutdown (it can save your time but may damage your
    data).

* Example Response
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get broker cluster]({{< relref "#get" >}}) to fetch up-to-date status
{{% /alert %}}

## add a new node to a running broker cluster

*PUT /v0/brokers/$name/$nodeName?group=$group*

If you want to extend a running broker cluster, you can add a node to
share the heavy loading of a running broker cluster. However, the
balance is not triggered at once.

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    ```http request
    202 Accepted
    ```

{{% alert note %}}
Although it's a rare case, you should not use the "API keyword"
as the nodeName. For example, the following APIs are invalid and
will trigger different behavior!
- /v0/brokers/$name/start
- /v0/brokers/$name/stop
{{% /alert %}}

## remove a node from a running broker cluster

*DELETE /v0/brokers/$name/$nodeName?group=$group*

If your budget is limited, you can decrease the number of nodes running
broker cluster. BUT, removing a node from a running broker cluster
invoke a lot of data move. The loading may burn out the remaining nodes.

We will use the default value as the query parameter "?group=" if you
don't specify it.

* Example Response
    ````
    204 NoContent
    ````
    
{{% alert hint %}}
It is ok to delete an nonexistent broker node, and the response is 204 NoContent.
{{% /alert %}}
