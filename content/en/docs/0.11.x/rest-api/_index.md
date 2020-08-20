---
title: Ohara REST Interface
linktitle: API Overview
layout: docs
draft: false
menu:
  011x:
    parent: REST APIs
    weight: 10

# Optional header image (relative to `static/img/` folder).
header:
  caption: ""
  image: ""
---

Ohara provides a bunch of REST APIs of managing data, applications and
cluster for Ohara users. Both request and response must have
application/json content type, hence you should set content type to
application/json in your request.

  > Content-Type: application/json

and add content type of the response via the HTTP Accept header:

 > Accept: application/json


**Statuses & Errors**

Ohara leverages [akka http](https://github.com/akka/akka-http) to support standards-compliant HTTP statuses.
your clients should check the HTTP status before parsing response
entities. The error message in response body are format to json content.

```json
{
 "code": "java.lang.IllegalArgumentException",
 "message": "Unsupported restful api:vasdasd. Or the request is invalid to the vasdasd",
 "stack": "java.lang.IllegalArgumentException: Unsupported restful api:vasdasd. Or the request is invalid to the vasdasd at"
}
```

- code (**string**) — the type of error. It is normally a type of java exception.
- message (**string**) — a brief description of error
- stack (**string**) — error stack captured by server


**Manage clusters**

  You are tired to host a bunch of clusters when you just want to build a
  pure streaming application. So do we! Ohara aims to take over the heavy
  management and simplify your life. Ohara leverage the docker technology
  to run all process in containers. If you are able to use k8s, Ohara is
  good at deploying all containers via k8s. If you are too afraid to touch
  k8s, Ohara is doable to be based on ssh connection to control all
  containers.

  Ohara automatically configure all clusters for you. Of course, you have
  the freedom to overwrite any settings. see section
  [zookeeper]({{< relref "./zookeepers.md" >}}), [broker]({{< relref "./brokers.md" >}}) and
  [worker]({{< relref "./workers.md" >}}) to see more details.

  In order to provide a great experience in exercising containers, Ohara
  pre-builds a lot of docker images with custom scripts. Of course, Ohara
  APIs allow you to choose other image instead of Ohara official images.
  However, it works only if the images you pick up are compatible to Ohara
  command. see [here]({{< relref "../docker.md" >}}) for more details. Also, all official
  images are hosted by [docker hub](https://cloud.docker.com/u/oharastream/repository/list)



