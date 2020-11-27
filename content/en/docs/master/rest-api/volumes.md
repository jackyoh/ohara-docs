---
title: Volume
linktitle: Volume API
toc: true
type: docs
data: "2020-11-27T00:00:00+01:00"
draft: false
menu:
  master:
    parent: REST APIs
    weight: 200
---

Ohara volume feature is persisting data by zookeeper container and broker container.

Note: Confirm your user name is ohara and UUID is 1000 in the running cluster environment.

The properties which can be set by user are shown below.

1. name (**String**) --- volume name. The legal character is lowercase alphanumerics characters and number.

2. group (**String**) --- volume group. The legal character is lowcase alphanumerics characters and number.

3. path (**String**) --- The data folder path in the node.

4. nodeNames (**Set[String]**) --- Setting the node to create ohara volume for the zookeeper and broker container use.

5. tags (**Object**) --- the user defined parameters.  


## create a volume properties {#create-properties}

*POST /v0/volumes*

* Example Request
    ```json
    {
      "name": "volume",
      "nodeNames": [
        "node00"
      ],
      "path": "/tmp/workspace"
    }
    ```

* Example Response
    ```json
    {
      "group": "default",
      "lastModified": 1606457846012,
      "name": "volume1",
      "nodeNames": [
        "node00"
      ],
      "path": "/tmp/workspace",
      "tags": {}
    }
    ```

{{% alert hint %}}
The /tmp/workspace data folder own MUST set ohara user name and UID is 1000

## start a ohara volume

*PUT /v0/volumes/$name/start?group=$group*

We will use the default value as the query parameter "?group=" if you don't specify it.

* Example Resposne
    ```
    202 Accepted
    ```

{{% alert hint %}}
You should use [Get volume info]({{< relref "#get" >}}) to show volume up-to-date status.

## get a volume info {#get}

*GET /v0/volumes/$name?group=$group*

Get volume info by name and group. This API could fetch all volume info.
We will use the default value as the query parameter "?group=" if you don't
specify it.

* Example Response
    ```json
    {
      "group": "default",
      "lastModified": 1606457846012,
      "name": "volume1",
      "nodeNames": [
        "k8s-master"
      ],
      "path": "/tmp/workspace1/bk12345",
      "state": "RUNNING",
      "tags": {}
     }
     ```

## add a volume to a running volumes

*PUT /v0/volumes/$name/$nodeName?group=$group*

Add a new volume for other node. We will use the default value as the query parameter "?group=" if you don't specify it.

* Example Resposne
    ```
    202 Accepted
    ```

## update volume properties setting

*PUT /v0/volumes/$name?group=$group*

If the required volume (group, name) was not exists, we will try to use this request as POST.

* Example Request
    ```json
    {
      "nodeNames": [
        "node00", "node01"
      ],
      "path": "/tmp/workspace2"
    }
    ```
* Example Response
    ```json
    {
      "group": "default",
      "lastModified": 1606465299352, 
      "name": $name,
      "nodeNames": ["node00", "node01"],
      "path": "/tmp/workspace2",
      "tags": {}
    }
    ```
## stop a volume info

Stop a running volume and remove the volume data folder (dangerous). It is disallowed to stop a volume cluster used by a running.

*PUT /v0/volumes/$name/stop?group=$group[&force=true]*

We will use the default value as the query parameter "?group=" if you don't specify it.

* Query Parameters
  1. force (**boolean**) - true if you don't want to wait the graceful
     shutdown (it can save your time but may damage your data).

* Example Response
    ```
    202 Accepted
    ```

## delete a volume properties

*DELETE /v0/volumes/$name?group=$group*

After the stop volume, you could delete volume setting.

* Example Response
    ```
    202 Accepted
    ```

