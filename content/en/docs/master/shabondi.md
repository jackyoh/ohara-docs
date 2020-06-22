---
title: Shabondi
linktitle: Shabondi
toc: true
type: docs
date: "2020-06-16T00:00:00+01:00"
draft: false
diagram: true
menu:
  master:
    parent: Contents
    weight: 70
---

Shabondi service play the role of a _http proxy service_ in the Pipeline
of Ohara. If you want to integrate Ohara pipeline with your application,
Shabondi is a good choice. Just send the simple http request to
**Shabondi source** service, you can hand over your data to Pipeline for
processing. On the other hand, you can send the http request to
**Shabondi sink** service to fetch the output data of the Pipeline.

Following is a simple diagram of Pipeline to demonstrate about both the source and sink of Shabondi:

{{< figure src="../img/shabondi-pipeline.png" title="Shabondi Pipeline" lightbox="false" >}}


## Data format

Both Shabondi source and sink use [Row]({{< relref "custom_connector.md#datamodel" >}}) in JSON format 
for data input and output. Row is a table structure data defined in Ohara code base. 
A row is comprised of multiple cells. Each cell has its **name** and **value**. 
Every row of your input data will be stored in the **Topic**.

A table:

row #    | name  | age | email             | career
---------|-------|-----|-------------------|--------
 (row 1) | jason | 42  | jason@example.com | Surgeon
 ...     | ...   | ... | ...               | ...
 (row n) | ...   | ... | ...               | ...
  
 
Example of row using Java:
```java
import oharastream.ohara.common.data.Row;
import oharastream.ohara.common.data.Cell;
class ExampleOfRow {
    public static void main(String[] args) {
        Row row = Row.of(
                Cell.of("name", "jason"),
                Cell.of("age", "42"),
                Cell.of("email", "jason@example.com"),
                Cell.of("career", "Surgeon")
                );
    }
}
```

Example of row in JSON format

```json
{
  "name": "jason",
  "age": 42,
  "email": "jason@example.com",
  "career": "Surgeon"
}
```

## Deployment

There are two ways to deploy Shabondi service

- Ohara Manager: (TBD)
- Use Configurator [REST API]({{< relref "rest-api/shabondis.md" >}}) to create 
  and start Shabondi service.

After a Shabondi service is properly configured, deploy and successfully started. 
It's ready to receive or send requests via HTTP.

## Service REST API

Shabondi source service receives single row message through HTTP
requests and then writes to the connected **Topic**.

### Source API

#### Send Row

Send a JSON data of [Row]({{< relref "custom_connector.md#datamodel" >}}) to Shabondi source service.

* Request  
  POST /

* Request body  
  The row data in JSON format

* Example 1 (Succeed)
  * Request

    ```http request
    POST http://node00:58456 HTTP/1.1
    Content-Type: application/json

    {
      "name": "jason",
      "age": 42,
      "email": "jason@example.com",
      "career": "Surgeon"
    }
    ```

  * Response

    ```http request
    HTTP/1.1 200 OK
    Server: akka-http/10.1.11
    Date: Tue, 19 May 2020 02:41:20 GMT
    Content-Type: text/plain; charset=UTF-8
    Content-Length: 2

    OK
    ```

* Example 2 (Failure)
  * Request

    ``` {.http}
    GET http://node00:58456 HTTP/1.1
    ```

  * Response

    ```http request
    HTTP/1.1 405 Method Not Allowed
    Server: akka-http/10.1.11
    Date: Tue, 19 May 2020 02:45:56 GMT
    Content-Type: text/plain; charset=UTF-8
    Content-Length: 90
    
    Unsupported method, please reference: https://oharastream.github.io/docs/master/shabondi/
    ```

### Sink API

The Shabondi Sink service accepts the http request, and then reads the
rows from the connected **Topic** and response it in JSON format.

#### Fetch Rows

* Request  
  GET /groups/\$groupName

* Response  
  The array of row in JSON format

* Example 1 (Succeed)
  * Request

    ```http request
    GET http://node00:58458/groups/g1 HTTP/1.1
    ```

  * Response

    ```http request
    HTTP/1.1 200 OK
    Server: akka-http/10.1.11
    Date: Wed, 20 May 2020 06:18:44 GMT
    Content-Type: application/json
    Content-Length: 115
    
    [
      {
        "name": "jason",
        "age": 42,
        "email": "jason@example.com",
        "career": "Surgeon"
      },
      {
        "name": "robert",
        "age": 36,
        "email": "robert99@gmail.com",
        "career": "Teacher"
      }
    ]
    ```

* Example 2 - Failure response(Illegal group name)
  * Request

    ```http request
    GET http://node00:58458/groups/g1-h HTTP/1.1
    ```
    
  * Response
    
    ```http request
    HTTP/1.1 406 Not Acceptable
    Server: akka-http/10.1.11
    Date: Wed, 20 May 2020 07:34:10 GMT
    Content-Type: text/plain; charset=UTF-8
    Content-Length: 50
    
    Illegal group name, only accept alpha and numeric.
    ```
