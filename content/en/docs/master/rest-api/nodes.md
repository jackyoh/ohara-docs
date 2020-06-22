---
title: Node
linktitle: Node
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 120
oharaVersion: 0.10.0
---

Node is the basic unit of running service. It can be either physical
machine or vm. In section
[zookeeper]({{< relref "zookeepers.md" >}}),
[Broker]({{< relref "brokers.md" >}}) and
[Worker]({{< relref "workers.md" >}}), you will see many
requests demanding you to fill the node name to build the services.
Currently, Ohara requires the node added to Ohara should pre-install
following services.

1. docker (18.09+)
2. ssh server
3. k8s (only if you want to k8s to host containers)
4. official Ohara images
   - [oharastream/zookeeper](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/zookeeper)
   - [oharastream/broker](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/broker)
   - [oharastream/connect-worker](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/connect-worker)
   - [oharastream/stream](https://cloud.docker.com/u/oharastream/repository/docker/oharastream/stream)

The version (tag) depends on which Ohara you used. It would be better to
use the same version to Ohara. For example, the version of Ohara
configurator you are running is {{< param oharaVersion >}}, then the official images you should
download is `oharastream/xxxx:{{< param oharaVersion >}}`.

The properties used by describing a node are shown below.

1. hostname (**string**) --- hostname of node. The legal character is
   number, lowercase alphanumeric characters, or '.'
   
   This hostname must be available on you DNS. It will cause a lot of
   troubles if Ohara Configurator is unable to connect to remote node
   via this hostname.
2. port (**int**) --- ssh port of node
3. user (**string**) --- ssh account
4. password (**string**) --- ssh password
5. tags (**object**) --- the extra description to this object

The following information are updated at run-time.

1. services (**array(object)**) --- the services hosted by this node
   - services[i].name (**string**) --- service name (configurator,
     zookeeper, broker, connect-worker and stream)
   - services[i].clusterKeys (**array(object)**) --- the keys of
     this service
2. resources (**array(object)**) --- the available resources of this
   node
   - resources[i].name (**string**) --- the resource name
   - resources[i].value (**number**) --- the "pure" number of
     resource
   - resources[i].used (**option(double)**) --- the used "value"
     in percentage. Noted: this value may be null if the impl is
     unable to calculate the used resource.
   - resources[i].unit (**string**) --- the description of the
     "value" unit
3. state (**String**) --- "available" means this node works well.
   otherwise, "unavailable" is returned
4. error (**option(String)**) --- the description to the unavailable
   node
5. lastModified (**long**) --- the last time to update this node

## store a node

*POST /v0/nodes*

1. hostname (**string**) --- hostname of node
2. port (**int**) --- ssh port of node
3. user (**string**) --- ssh account
4. password (**string**) --- ssh password

* Example Request
    ```json
    {
      "hostname": "node00",
      "port": 22,
      "user": "abc",
      "password": "pwd"
    }
    ```

* Example Response
    ```json
    {
      "services": [
        {
          "name": "zookeeper",
          "clusterKeys": []
        },
        {
          "name": "broker",
          "clusterKeys": []
        },
        {
          "name": "connect-worker",
          "clusterKeys": []
        },
        {
          "name": "stream",
          "clusterKeys": []
        },
        {
          "name": "configurator",
          "clusterKeys": [
            {
                "group": "N/A",
                "name": "node00"
            }
          ]
        }
      ],
      "hostname": "node00",
      "state": "AVAILABLE",
      "lastModified": 1578627668686,
      "tags": {},
      "port": 22,
      "resources": [
        {
          "name": "CPU",
          "value": 6.0,
          "unit": "cores"
        },
        {
          "name": "Memory",
          "value": 10.496479034423828,
          "unit": "GB"
        }
      ],
      "user": "abc",
      "password": "pwd"
    }
    ```

## update a node

*PUT /v0/nodes/${name}*

1. hostname (**string**) --- hostname of node
2. port (**int**) --- ssh port of node
3. user (**string**) --- ssh account
4. password (**string**) --- ssh password

* Example Request
    ```json
    {
      "port": 9999
    }
    ```

{{% alert hint %}}
An new node will be created if your input name does not exist
{{% /alert %}}

{{% alert hint %}}
the update request will clear the validation report attached to this
node
{{% /alert %}}

* Example Response
    ```json
    {
      "services": [
        {
          "name": "zookeeper",
          "clusterKeys": []
        },
        {
          "name": "broker",
          "clusterKeys": []
        },
        {
          "name": "connect-worker",
          "clusterKeys": []
        },
        {
          "name": "stream",
          "clusterKeys": []
        },
        {
          "name": "configurator",
          "clusterKeys": [
            {
                "group": "N/A",
                "name": "node00"
            }
          ]
        }
      ],
      "hostname": "node00",
      "state": "AVAILABLE",
      "lastModified": 1578627668686,
      "tags": {},
      "port": 9999,
      "resources": [
        {
          "name": "CPU",
          "value": 6.0,
          "unit": "cores"
        },
        {
          "name": "Memory",
          "value": 10.496479034423828,
          "unit": "GB"
        }
      ],
      "user": "abc",
      "password": "pwd"
    }
    ```

## list all nodes stored in Ohara

*GET /v0/nodes*

* Example Response
    ```json
    [
      {
        "services": [
          {
            "name": "zookeeper",
            "clusterKeys": []
          },
          {
            "name": "broker",
            "clusterKeys": []
          },
          {
            "name": "connect-worker",
            "clusterKeys": []
          },
          {
            "name": "stream",
            "clusterKeys": []
          },
          {
            "name": "configurator",
            "clusterKeys": [
              {
                  "group": "N/A",
                  "name": "node00"
              }
            ]
          }
        ],
        "hostname": "node00",
        "state": "AVAILABLE",
        "lastModified": 1578627668686,
        "tags": {},
        "port": 22,
        "resources": [
          {
            "name": "CPU",
            "value": 6.0,
            "unit": "cores"
          },
          {
            "name": "Memory",
            "value": 10.496479034423828,
            "unit": "GB"
          }
        ],
        "user": "abc",
        "password": "pwd"
      }
    ]
    ```

## delete a node

*DELETE /v0/nodes/${name}*

* Example Response
    ````
    204 NoContent
    ````

{{% alert hint %}}
It is ok to delete a nonexistent pipeline, and the response is
204 NoContent. However, it is disallowed to remove a node which is
running service. If you do want to delete the node from Ohara,
please stop all services from the node.
{{% /alert %}}

## get a node

*GET /v0/nodes/${name}*

* Example Response
    ```json
    {
      "services": [
        {
          "name": "zookeeper",
          "clusterKeys": []
        },
        {
          "name": "broker",
          "clusterKeys": []
        },
        {
          "name": "connect-worker",
          "clusterKeys": []
        },
        {
          "name": "stream",
          "clusterKeys": []
        },
        {
          "name": "configurator",
          "clusterKeys": [
            {
                "group": "N/A",
                "name": "node00"
            }
          ]
        }
      ],
      "hostname": "node00",
      "state": "AVAILABLE",
      "lastModified": 1578627668686,
      "tags": {},
      "port": 22,
      "resources": [
        {
          "name": "CPU",
          "value": 6.0,
          "unit": "cores"
        },
        {
          "name": "Memory",
          "value": 10.496479034423828,
          "unit": "GB"
        }
      ],
      "user": "abc",
      "password": "pwd"
    }
    ```
