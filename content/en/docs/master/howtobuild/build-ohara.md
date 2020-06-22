---
title: How to build Ohara
linktitle: How to build Ohara
toc: true
type: docs
date: "2020-06-15T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Contents
    weight: 100    
---

## Prerequisites

- OpenJDK 11 
- Scala 2.13.2
- Gradle 6.5
- Node.js 8.12.0
- Yarn 1.13.0 or greater
- Docker 19.03.8 (Docker multi-stage, which is supported by Docker 17.05 or higher, is required in building ohara images. see [Use multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) for more details)
- Kubernetes 1.18.1 (Official QA uses the Kubernetes version is 1.18.1)

----

## Gradle Commands

Ohara build is based on [gradle](https://gradle.org/). Ohara has defined many gradle tasks to simplify the development of Ohara.

### Build Binary {#build-binary}

```console
$ ./gradlew clean build -x test
```

{{% alert hint %}}
 the tar file is located at ohara-${module}/build/distributions
{{% /alert %}}


### Run All UTs

```console
$ ./gradlew clean test
```

{{% alert note %}}
Ohara IT tests requires specific envs, and all IT tests will be skipped if you don't pass the related setting to IT. Ohara recommends you testing your code on [official QA](https://builds.is-land.com.tw/job/PreCommit-OHARA/) which offers the powerful machine and IT envs.
{{% /alert %}}

[//]: <> (TODO: fix the broken link)

{{% alert hint %}}
add flag "-PskipManager" to skip the tests of Ohara Manager. Ohara Manager is a module in charge of Ohara UI. Feel free to skip it if you are totally a backend developer. By the way, the prerequisites of testing Ohara Manager is shown in `here <managerdev>`
{{% /alert %}}


{{% alert hint %}}
add flag "-PmaxParallelForks=6" to increase the number of test process to start in parallel. The default value is number of cores / 2, and noted that too many tests running in parallel may easily
produce tests timeout.
{{% /alert %}}


### Code Style Auto-Apply

Use this task to make sure your added code will have the same format and conventions with the rest of codebase.

``` {.console}
$ ./gradlew spotlessApply
```

{{% alert hint %}}
we have this style check in early QA build.
{{% /alert %}}


### License Auto-Apply

If you have added any new files in a PR. This task will automatically insert an Apache 2.0 license header in each one of these newly created files

```console
$ ./gradlew licenseApply
```

{{% alert hint %}}
Note that a file without the license header will fail at early QA build
{{% /alert %}}


### Publish Artifacts

```console
$ ./gradlew clean bintrayUpload -PskipManager -PbintrayUser=$user -PbintrayKey=$key
```

{{% alert note %}}
-   bintrayUser: the account that has write permission to the
    repository
-   bintrayKey: the account API Key
-   public: whether to auto published after uploading. default is
    false
-   override: whether to override version artifacts already
    published. default is false
{{% /alert %}}

> Note: Only release manager has permission to upload artifacts


### Publish Artifacts


```console
$ ./gradlew clean build publishToMavenLocal -PskipManager -x test
```


## Installation

see [User Guide]({{< relref "../user_guide.md" >}})
