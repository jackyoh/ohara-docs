---
title: Write content example
toc: true
type: docs
date: "2020-06-15T00:00:00+01:00"
draft: false
---

## Shortcode

### Hugo & Academic

* https://sourcethemes.com/academic/docs/writing-markdown-latex/
* https://gohugo.io/content-management/shortcodes/

### Custom by Ohara

* Branch: {{< branch >}}
* Version: {{< ohara-version >}}
* Ohara file link: [backend docker file]({{< ohara-file "/docker/backend.dockerfile" >}})
* Ohara issue link: [#5266]({{< ohara-issue 5266 >}})
* Ohara file content:
{{< ohara-file-content file="/ohara-common/src/main/java/oharastream/ohara/common/data/Row.java" lang="java" options="linenos=table,hl_lines=85" >}}
