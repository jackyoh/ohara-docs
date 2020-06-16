---
title: Contributing
linktitle: Contributing
toc: true
type: docs
date: "2020-06-15T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Contents
    weight: 900
---


All we love is only pull request so we have some rules used to make your
PR looks good for reviewers.

{{% alert note %}}
Note that you should file a new issue to discuss the PR detail with us
before submitting a PR.
{{% /alert %}}

## Quick start

- Fork and clone the [oharastream/ohara](https://github.com/oharastream/ohara) repo
- Install dependencies. See our [how_to_build_Ohara]({{< ref "howtobuild/build-ohara.md" >}}) for development machine setup
- Create a branch with your PR with:  
  `git checkout -b ${your-branch-name}`
- Push your PR to remote:  
  `git push origin ${your-branch-name}`
- Create the PR with GitHub web UI and wait for reviews from our committers

## Testing commands in the pull request

These commands will come in handy when you want to test your PR on our QA(CI server). To start a QA run, you can simply leave a comment with one of the following commands in the PR:

{{% alert hint %}}
Note that the comment should contain the exact command as listed below,
comments like **Please retry my PR** or **Bot, retry -fae** won\'t work:
{{% /alert %}}

- `retry`  
  trigger a full QA run
- `retry -fae`  
  trigger a full QA run even if there's fail test during the run
- `retry ${moduleName}`  
  trigger a QA run for a specific module. If a module is named **ohara-awesome**, you can enter `retry awesome` to run the QA against this specific module. Note that the module prefix **ohara-** is not needed. Following are some examples:  
  - `retry manager`: run **ohara-manager**'s unit test.
  - `retry configurator`: run **ohara-configurator**'s unit test.
    
  Ohara manager has a couple of different tests and can be run separately by using the above-mentioned `retry` command.
    - `retry manager-api`: run manager's API tests
    - `retry manager-ut`: run manager's unit tests
    - `retry manager-e2e`: run manager's end-to-end tests
- `run`: start both Configurator and Manager on jenkins server. If the specified PR makes some changes to UI, you can run this command to see the changes.

The QA build status can be seen at the bottom of your PR.

## Important things about pull request

**A pull request must...**

- Pass all tests
- Your PR should not make ohara unstable, if it does. It should be reverted ASAP.
- You can either run these tests on your local (see our [how_to_build_Ohara]({{< ref "howtobuild/build-ohara.md" >}}) for more info on how to run tests) or by opening the PR on our repo. These tests
  will be running on your CI server.
- Pass code style check. You can automatically fix these issues with a
  single command:

  ```console
  $ ./gradlew spotlessApply
  ```
- Address all reviewers' comments

**A pull request should...**

- Be as small in scope as possible. Large PR is often hard to review.
- Add new tests

**A pull request should not...**

- Bring in new libraries (or updating libraries to new version) without prior discussion. Do not update the dependencies unless you have a good reason.
- Bring in the new module without prior discussion
- Bring in new APIs for Configurator without prior discussion
