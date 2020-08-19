---
title: Validation
linktitle: Validation API
toc: true
type: docs
date: "2020-06-18T00:00:00+01:00"
draft: false
menu:
  master:
    parent: REST APIs
    weight: 180
---

Notwithstanding we have read a lot of document and guideline, there is a
chance to input incorrect request or settings when operating Ohara.
Hence, Ohara provides a serial APIs used to validate request/settings
before you do use them to start service. Noted that not all
request/settings are validated by Ohara configurator. If the
request/settings is used by other system (for example, kafka), Ohara
automatically bypass the validation request to target system and then
wrap the result to JSON representation.

## Validate the connector settings

*PUT /v0/validate/connector*

Before starting a connector, you can send the settings to test whether
all settings are available for the specific connector. Ohara is not in
charge of settings validation. Connector MUST define its setting via
[setting definitions]({{< relref "../setting_definition.md" >}}). 
Ohara configurator only repackage the request to kafka
format and then collect the validation result from kafka.

* Example Request

    The request format is same as [connector request]({{< relref "./connectors.md#create-settings" >}})

* Example Response

    If target connector has defined the settings correctly, kafka is
    doable to validate each setting of request. Ohara configurator
    collect the result and then generate the following report.

    ```json
    {
      "errorCount": 0,
      "settings": [
        {
          "definition": {
            "displayName": "Connector class",
            "group": "core",
            "orderInGroup": 3,
            "key": "connector.class",
            "valueType": "CLASS",
            "necessary": "REQUIRED",
            "defaultValue": null,
            "documentation": "the class name of connector",
            "reference": "NONE",
            "regex": null,
            "internal": false,
            "permission": "EDITABLE",
            "tableKeys": [],
            "recommendedValues": [],
            "blacklist": []
          },
          "value": {
            "key": "connector.class",
            "value": "oharastream.ohara.connector.perf.PerfSource",
            "errors": []
          }
        }
      ]
    }
    ```

    The above example only show a part of report. The element **definition**
    is equal to
    [connector’s setting definition]({{< relref "./workers.md" >}}). 
    The definition is what connector must define. If you don't
    write any definitions for you connector, the validation will do nothing
    for you. The element **value** is what you request to validate.
    
    1. key (**string**) --- the property key. It is equal to key in **definition**
    2. value (**string**) --- the value you request to validate
    3. errors (**array(string)**) --- error message when the input value is
       illegal to connector
