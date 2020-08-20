---
title: Setting Definition Guide
linktitle: Setting Definition Guide
toc: true
type: docs
date: "2020-06-17T00:00:00+01:00"
draft: false
diagram: true
menu:
  011x:
    parent: Contents
    weight: 60
---

A powerful application always has a complicated configuration. In order
to be a best friend of Ohara users, Ohara provides a method which can
return the details of setting definitions, and ohara suggests that all
developers ought to implement the method so as to guide users through
the complicated settings of your applications.

{{% alert note %}}
If you have no interest in settings or your application is too simple to
have any settings, you can just skip this section.
{{% /alert %}}

SettingDef is a class used to describe the details of **a** setting. It
consists of following arguments.

1. [reference]({{< relref "#setting-reference" >}})(**string**) --- works for ohara manager. 
   It represents the reference of value.
1. group (**string**) --- the group of this setting (all core setting are in core group)
1. orderInGroup (**int**) --- the order in group
1. displayName (**string**) --- the readable name of this setting
1. permission (**string**) --- the way to "touch" value. It consists of
    - READ_ONLY --- you can't define an new value for it
    - CREATE_ONLY --- you can't update the value to an new one
    - EDITABLE --- feel free to modify the value as you wish :)
1. key (**string**) --- the key of configuration
1. [valueType]({{< relref "#value-type" >}})(**string**) --- the type of value
1. necessary (**string**)
    - REQUIRED --- this field has no default and user MUST define something for it.
    - OPTIONAL --- this field has no default and user does NOT need to define something for it.
    - RANDOM_DEFAULT --- this field has a "random" default value
1. defaultValue (**object**) --- the default value. the type is equal to what valueType 
   defines but we only allow string, number and boolean type to have default value currently.
1. documentation (**string**) --- the explanation of this definition
1. internal (**boolean**) --- true if this setting is assigned by system automatically.
1. tableKeys (**array(object)**) --- the description to Type.TABLE
    - tableKeys[i].name --- the column name
    - tableKeys[i].type --- acceptable type (string, number and boolean)
    - tableKeys[i].recommendedValues --- recommended values 
      (it is legal to enter other values you prefer)

{{% alert hint %}}
You can call [Worker APIs]({{< relref "rest-api/workers.md" >}})
to get all connectors' setting definitions, and use [Stream APIs]({{< relref "rest-api/streams.md" >}}) 
to get all stream setting definitions.
{{% /alert %}}

Although a SettingDef can include many elements, you can simply build a
SettingDef with only what you need. An extreme example is that you can
create a SettingDef with only key.

```java
SettingDef.builder()
    .key(key)
    .build();
```

Notwithstanding it is flexible to build a SettingDef, we encourage
developers to create a description-rich SettingDef. More description to
your setting produces more **document** in calling ohara rest APIs. We
all hate write documentation, so it would be better to make your code
readable.

## Reference, Internal and TableKeys Are NOT Public

Ohara offers a great UI, which is located at ohara-manager. The UI
requires some **private** information to generate forms for custom
applications. The such private information is specific-purpose and is
meaningless to non-ohara developers. Hence, all of them are declared as
package-private and ohara does not encourage developers to stop at
nothing to use them.

## Optional, Required And Default Value

We know a great application must have countless settings and only The
Chosen One can control it. In order to shorten the gap between your
application and human begin, ohara encourage developers to offer the
default values to most of settings as much as possible. Assigning a
default value to a SettingDef is a piece of cake.

```java
SettingDef.builder()
    .key(key)
    .optional(defaultValue)
    .build();
```

{{% alert hint %}}
the default value is declared as **string** type as it must be **readable** in Restful APIs.
{{% /alert %}}

After calling the **optional(String)** method, the response, created by
[Worker APIs]({{< relref "rest-api/workers.md" >}}) for example,
will display the following information.

```json
{
  "necessary": "OPTIONAL_WITH_DEFAULT",
  "defaultValue": "ur_default_value"
}
```

{{% alert hint %}}
The default value will be added to
[TaskSetting]({{< relref "./custom_connector.md#source-start" >}})
automatically if the specified key is not already associated with a value.
{{% /alert %}}


## A Readonly Setting Definition

You can declare a **readonly** setting that not only exposes something
of your application to user but also remind user the setting can't be
changed at runtime. For instance, the information of
[version]({{< relref "./custom_connector.md#version" >}}) is fixed
after you have completed your connector so it is not an **editable**
setting. Hence, ohara define a setting for **version** with a readonly
label. By the way, you should assign a default value to a readonly
setting since a readonly setting without default value is really weird.
There is an example of creating a readonly setting.

```java
SettingDef.builder()
    .key(key)
    .optional(defaultValue)
    .permission(SettingDef.Permission.READ_ONLY)
    .build();
```

{{% alert hint %}}
The input value will be removed automatically if the associated setting
is declared readonly.
{{% /alert %}}


## Setting Reference

This element is a specific purpose. It is used by Ohara manager (UI) only. 
If you don't have interest in UI, you can just ignore this element. 
However, we still list the available values here.

* NODE
* TOPIC
* ZOOKEEPER
* BROKER
* WORKER

{{% alert hint %}}
For each reference value, it may have different type and will produce
different behavior.
{{% /alert %}}

Topic String

```java
SettingDef.builder().key("topic").reference(Reference.TOPIC).required(Type.STRING).build();
```

which means the request should "accept one topic of string type"

```json
{
  "topic": "t1"
}
```

------------------------------------------------------------------------

TopicKey List

```java
SettingDef.builder()
    .key("topicKeys")
    .reference(Reference.TOPIC)
    .required(Type.OBJECT_KEYS)
    .build();
```

which means the request should "accept topic list of **TopicKey** type"

```json
{
  "topicKeys": [
    {
      "group": "default",
      "name": "t1"
    },
    {
      "group": "default",
      "name": "t2"
    }
  ]
}
```

------------------------------------------------------------------------

Topic String List

```java
SettingDef.builder()
    .key("topics")
    .reference(Reference.TOPIC)
    .required(Type.ARRAY)
    .build();
```

which means the request should "accept topic list of string type"

```json
{
  "topics": ["t1", "t2", "t3"]
}
```

## Value Type

In a custom application, the settings could have various data type. In
order to display correct data type in ohara manager and leverage the
benefit of [type checker]({{< relref "#checker" >}}), we
strongly suggest you to define the correct data type for each setting.

The following data types are supported currently.

#### Type.BOOLEAN

Boolean type represents that the data should have only two possible
value: **true** or **false**. The value must be able cast to
**java.lang.Boolean**

#### Type.STRING

String type represents that the data should be a string. The value must
be able cast to **java.lang.String**

```java
SettingDef.builder()
    .key(key)
    .required(Type.STRING)
    .build();
```

#### Type.POSITIVE_SHORT

Short type represents that the data should be a 2-bytes integer. The
value must be able cast to **java.lang.Short**. Noted: only positive
number is acceptable

```java
SettingDef.builder()
    .key(key)
    .required(Type.POSITIVE_SHORT)
    .build();
```

#### Type.SHORT

Short type represents that the data should be a 2-bytes integer. The
value must be able cast to **java.lang.Short**

```java
SettingDef.builder()
    .key(key)
    .required(Type.SHORT)
    .build();
```

#### Type.POSITIVE_INT

Int type represents that the data should be a 4-bytes integer. The value
must be able cast to **java.lang.Integer**. Noted: only positive number
is acceptable

```java
SettingDef.builder()
    .key(key)
    .required(Type.POSITIVE_INT)
    .build();
```

#### Type.INT

Int type represents that the data should be a 4-bytes integer. The value
must be able cast to **java.lang.Integer**

```java
SettingDef.builder()
    .key(key)
    .required(Type.INT)
    .build();
```

#### Type.POSITIVE_LONG

Long type represents that the data should be a 8-bytes integer. The
value must be able cast to **java.lang.Long**. Noted: only positive
number is acceptable

```java
SettingDef.builder()
    .key(key)
    .required(Type.POSITIVE_LONG)
    .build();
```

#### Type.LONG

Long type represents that the data should be a 8-bytes integer. The
value must be able cast to **java.lang.Long**

```java
SettingDef.builder()
    .key(key)
    .required(Type.LONG)
    .build();
```

#### Type.POSITIVE_DOUBLE

Double type represents that the data should be a 8-bytes floating-point.
The value must be able cast to **java.lang.Double**. Noted: only
positive number is acceptable

```java
SettingDef.builder()
    .key(key)
    .required(Type.POSITIVE_DOUBLE)
    .build();
```

#### Type.DOUBLE

Double type represents that the data should be a 8-bytes floating point.
The value must be able cast to **java.lang.Double**

```java
SettingDef.builder()
    .key(key)
    .required(Type.DOUBLE)
    .build();
```

#### Type.ARRAY

Array type represents that the data should be a collection of data. We
don't check the element data type in the collection, that is, the
following request is legal in SettingDef but will produce a weird
behavior in ohara manager. We suggest you use the same data type of
element in an array.

```json
{
  "key": ["abc", 123, 2.0]
}
```

```java
SettingDef.builder()
    .key(key)
    .required(Type.ARRAY)
    .build();
```

{{% alert hint %}}
An empty array is ok and will pass the checker:

```json
{
  "key": []
}
```
{{% /alert %}}

{{% alert hint %}}
the default value to array value is empty
{{% /alert %}}

#### Type.CLASS

Class type represents that the data is a class. This data type is used
to display a value that is a class. The value must be able cast to
**java.lang.String**.

```java
SettingDef.builder()
    .key(key)
    .required(Type.CLASS)
    .build();
```

#### Type.PASSWORD

Password type represents that the data is a password. We will replace
the value by **hidden** symbol in APIs. if the data type is used as
password. The value must be able cast to **java.lang.String**.

```java
SettingDef.builder()
    .key(key)
    .required(Type.PASSWORD)
    .build();
```

#### Type.JDBC_TABLE

JDBC_TABLE is a specific string type used to reminder Ohara Manager
that this field requires a **magic** button to show available tables of
remote database via Query APIs. Except for the **magic** in UI, there is
no other stuff for this JDBC_TYPE since kafka can't verify the input
arguments according to other arguments. It means we can't connect to
remote database to check the existence of input table.

It is ok to replace this field by Type.STRING if you don't use Ohara
Manager. Nevertheless, we still encourage the developer to choose the
**fitting** type for your setting if you demand your user to input a
database table.

#### Type.TABLE

Table type enable you to define a setting having table structure value.
Apart from assigning Type.Table to your setting definition, you also
have to define which keys are in your table. The following example show
a case that declares a table having two columns called **c0** and
**c1**.

```java
SettingDef.builder()
    .key(key)
    .tableKeys(Arrays.asList("c0", "c1"))
    .required(Type.TABLE)
    .build();
```

The legal value for above setting definition is shown below.

```json
{
  "key": [
    {
      "c0": "v0",
      "c1": "v1"
    },
    {
      "c0": "v2",
      "c1": "v3"
    }
  ]
}
```

The above example implies there is a table having two columns called
**c0** and **c1**. Also, you assign two values to **c0** that first is
**v0** and another is **v2**. Ohara offers a check for Type.Table that
the input value **must** match all keys in.

How to get the description of above **keys** ? If the setting type is
**table**, the setting must have **tableKeys**. It is an array of string
which shows the keys used in the table type. For instance, a setting
having table type is shown below.

```json
{
  "reference": "NONE",
  "displayName": "columns",
  "internal": false,
  "documentation": "output schema",
  "valueType": "TABLE",
  "tableKeys": [
    "order",
    "dataType",
    "name",
    "newName"
  ],
  "orderInGroup": 6,
  "key": "columns",
  "necessary": "REQUIRED",
  "defaultValue": null,
  "group": "core",
  "permission": "EDITABLE"
}
```

{{% alert hint %}}
If you ignore the table keys for Type.Table, the check to your input
value is also ignored. By contrast, the table keys are useless for other
types.
{{% /alert %}}

{{% alert hint %}}
the default value to table value is empty
{{% /alert %}}

#### Type.DURATION

The time-based amount of time is a common setting in our world. However,
it is also hard to reach the consensus about the **string representation** 
for a duration. For instance, the `java.time.Duration`
prefers ISO-8601, such as PT10S. The scala.concurrent.duration.Duration 
prefers simple format, such as 10 seconds. Ohara offers a official
support to Duration type so as to ease the pain of using string in
connector. When you declare a setting with duration type, ohara provides
the default check which casts input value to java Duration and scala
Duration. Also, your connector can get the **Duration** from
[TaskSetting]({{< relref "./custom_connector.md#source-start" >}})
easily without worrying about the conversion between java and scala.
Furthermore, connector users can input both java.Duration and
scala.Duration when starting connector.

The value must be castable to **java.time.Duration** and it is based on
the ISO-860 duration format PnDTnHnMn.nS

#### Type.REMOTE_PORT

Remote port is a common property to connector. For example, the ftp
connector needs port used to connect to source/target ftp server in
remote . Inputting an illegal port can destroy connector easily.
Declaring your type of value to Port involve a check that only the port
which is smaller than 65536 and bigger than zero can be accepted. Other
port value will be rejected in starting connector.

#### Type.BINDING_PORT

This type is similar to Type.PORT except that the value mapped to
BINDING_PORT has an extra check to the availability on the target nodes.
For example, you define value 5555 as a BINDING_PORT, and you will get
a exception when you try to deploy your code on the node which is using
port 5555 as well. The legal value of binding port is between [0, 65535].

#### Type.OBJECT_KEY

object key represents a format of
**oharastream.ohara.common.setting.ObjectKey** for a specific object. It
consists "group" and "name" fields. In a custom application, you
should check the request contains both fields.

```json
{
  "key": {
    "group": "default",
    "name": "abc"
  }
}
```

#### Type.OBJECT_KEYS

OBJECT_KEYS represents a list of
**oharastream.ohara.common.setting.Obj**. Note the type of the plural
char "s". It means the request value should pass an array.

```json
{
  "objectKeys": [{
    "group": "default",
    "name": "t1"
  }]
}
```

{{% alert hint %}}
the default value to table value is empty
{{% /alert %}}

#### Type.TAGS

Tags is a flexible type that accept a json object. It could use in some
circumstances that user needs to define additional values which type is
not list above.

```json
{
  "tags": {
    "name": "hello",
    "anArray": ["bar", "foo"],
    "count": 10,
    "params": {
      "k": "v"
    }
  }
}
```

{{% alert hint %}}
the default value to table value is empty
{{% /alert %}}

## Necessary

In Ohara world, most components have a lot of configs to offers various
usage in production. In order to simplify the settings, most configs
have default value and you can trace Necessary field to know that.

Necessary field has three values.

1. **REQUIRED** --- this value has no default value, and it must be defined. 
   You may get error if you don't give any value to it.
1. **OPTIONAL** --- this value has no default value, but it is ok to leave nothing.
1. **RANDOM_DEFAULT** --- the default value assigned to this value is random. 
   For example, all objects' name has a random string by default; The binding 
   port field has a random free port by default.

## Checker

We all love quick failure, right? A quick failure can save our resource 
and time. Ohara offers many checks for your setting according to the
**expected** type. For example, a setting declared **Duration** type has
a checker which validate whether the input value is able to be cast to
either java.time.Duration or scala.duration.Duration. However, you are
going to design a complicated connector which has specific limit for
input value.

## DenyList

The denyList is useful information that it offers following checks.

1. The restful APIs will reject the values in the denyList
1. Ohara UI disable user to input the illegal words

Currently, denyList is used by Array type only.

## Recommended values

Recommended values is used by Ohara UI that it able to pop a list to
users when they are using UI.

Currently, recommended values is used by String type only.
