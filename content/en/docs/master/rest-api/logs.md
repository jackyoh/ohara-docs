---
title: Logs
linktitle: Logs API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  master:
    parent: REST APIs
    weight: 110
---

This world is beautiful but not safe. Even though Ohara shoulders the
blame for simplifying your life, there is a slim chance that something
don't work well in Ohara. The Logs APIs, which are engineers' best
friend, open a door to observe the logs of running cluster.

The available query parameters are shown below.

1. sinceSeconds (**long**) --- show the logs since relative time

It collects output from all containers' of a cluster and then format them
to JSON representation which has following elements.

1. clusterKey (**object**) --- cluster key
   - clusterKey.group (**string**) --- cluster group
   - clusterKey.name (**string**) --- cluster name
2. logs (**array(object)**) --- log of each container
   - logs[i].hostname --- hostname
   - logs[i].value --- total log of a container

## get the log of a running cluster

*GET /v0/logs/$clusterType/$clusterName?group=$group*

- clusterType (**string**)
  - zookeepers
  - brokers
  - workers
  - streams
  - shabondis

* Example Response
    ```json
    {
      "clusterKey": {
        "group": "default",
        "name": "zk"
      },
      "logs": [
        {
          "hostname": "node00",
          "value": "2020-01-14 10:15:42,146 [myid:] - INFO [main:QuorumPeerConfig@136"
        }
      ]
    }
    ```

## get the since seconds log

*GET /v0/logs/$clusterType/$clusterName?group=$group&sinceSeconds=$relativeSeconds*

* Example Response
    ```json
    {
      "clusterKey": {
        "group": "default",
        "name": "zk"
      },
      "logs": [
        {
          "hostname": "ohara-release-test-00",
          "value": "2020-01-15 02:00:43,090 [myid:] - INFO  [ProcessThread(sid:0 cport:2181)::PrepRequestProcessor@653] - Got user-level KeeperException when processing sessionid:0x100000761180000 type:setData cxid:0x11a zxid:0x9e txntype:-1 reqpath:n/a Error Path:/config/topics/default-topic0 Error:KeeperErrorCode = NoNode for /config/topics/default-topic0\n"
        }
      ]
    }
    ```

## get the log of Configurator

*GET /v0/logs/configurator*

* Example Response
    ```json
    {
      "clusterKey": {
        "group": "N/A",
        "name": "node00"
      },
      "logs": [
        {
          "hostname": "node00",
          "value": "2020-01-10 09:43:02,669 INFO  [main] configurator.Configurator$(391): start a configurator built on hostname:ohara-release-test-00 and port:5000\n2020-01-10 09:43:02,676 INFO  [main] configurator.Configurator$(393): enter ctrl+c to terminate the configurator"
        }
      ]
    }
    ```

{{% alert hint %}}
the Configurator MUST run on docker container and the node hosting
Configurator MUST be added to Configurator via
[Node APIs]({{< relref "./nodes.md" >}})
{{% /alert %}}
