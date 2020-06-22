---
title: Object
linktitle: Object
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  rest-api:
    parent: REST APIs
    weight: 130
oharaVersion: 0.10.0
---

Object APIs offer a way to store anything to Configurator. It is useful
when you have something temporary and you have no other way to store
them.

Similar to other APIs, the required fields are "name" and "group".

1. name (**string**) --- name of object. The legal character is number,
   lowercase alphanumeric characters, or '.'
2. group (**string**) --- group of object. The legal character is
   number, lowercase alphanumeric characters, or '.'
3. tags (**option(object)**) --- the extra description to this object

The following information are updated at run-time.

1. lastModified (**long**) --- the last time to update this node

## store a object

*POST /v0/objects*

* Example Request
    ```json
    {
      "name": "n0",
      "k": "v"
    }
    ```

* Example Response
    ```json
    {
      "name": "n0",
      "lastModified": 1579071742763,
      "tags": {},
      "k": "v",
      "group": "default"
    }
    ```

## update a object

*PUT /v0/objects/${name}*

* Example Request
    ```json
    {
      "k0": "v0"
    }
    ```

* Example Response
    ```json
    {
      "name": "n0",
      "k0": "v0",
      "lastModified": 1579072298657,
      "tags": {},
      "k": "v",
      "group": "default"
    }
    ```

## list all objects

*GET /v0/objects*

* Example Response
    ```json
    [
      {
        "name": "n0",
        "k0": "v1000000",
        "lastModified": 1579072345437,
        "tags": {},
        "k": "v",
        "group": "default"
      }
    ]
    ```

## delete a node

*DELETE /v0/objects/${name}*

* Example Response
    ```
    204 NoContent
    ```

## get a object

*GET /v0/objects/${name}*

* Example Response
    ```json
    {
      "name": "n0",
      "k0": "v0",
      "lastModified": 1579072345437,
      "tags": {},
      "k": "v",
      "group": "default"
    }
    ```
