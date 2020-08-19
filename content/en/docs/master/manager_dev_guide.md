---
title: Ohara Manager Development Guideline
linktitle: Ohara Manager Development Guideline
toc: true
type: docs
date: "2020-06-16T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Contents
    weight: 50
---

This module contains Ohara manager (an HTTP server powered by
[Node.js](https://nodejs.org/en/)) and Ohara manager client (A web-based
user interface built with [React.js](https://reactjs.org/) ). In the
following docs, we will refer **Server** as Ohara manager and **Client**
as Ohara manager client.

## Initial machine setup

1. Install [Node.js](https://nodejs.org/en/) 10.16.3 or
   node.js \>=10.16.3 and \< 13.0.0
2. Install [Yarn](https://yarnpkg.com/en/docs/install#mac-stable)
   1.13.0 or greater
3. Make sure you're in the ohara-manager root and use this command to
   setup the app: `yarn setup`. This will install all the dependencies
   for both the **Server** and **Client** as well as creating a
   production build for the client.
4. **Optional**: If you're using Visual Studio Code as your editor,
   have a look at our [Editors](#editors) section.

### Mac

Make sure you have `watchman` installed on your machine. You can do this
with homebrew:

```shell script
$ brew install watchman
```

### Linux

Install these dependencies for cypress:

```shell script
$ yum install -y xorg-x11-server-Xvfb gtk2-2.24* libXtst* libXScrnSaver* GConf2* alsa-lib*
```

## Development


{{% alert hint %}}
If this is your first time running this project, you need to complete
the [Initial machine setup](#initial-machine-setup) section above 👆
{{% /alert %}}

### Quick start guide

Make sure you're at the Ohara manager root, then start it with:

```shell script
$ yarn start --configurator ${http://host:port/v0}
```

Note that the configurator option is **required**, and you should have
configurator running before starting Ohara manager. You can see the
[user guide]({{< relref "./user_guide.md" >}}) on how to spin
up a Configurator

Open another terminal tab, and start the **Client**:

```shell script
$ yarn start:client
```

Now, go to http://localhost:3000 and start your development, happy
hacking 😎

### Full development guide

In development, you need to start both the **Ohara manager** and **Ohara
manager client** servers before you can start your development. Follow
the instructions below:

#### Server

Make sure you're at the ohara-manager root:

```shell script
$ yarn start --configurator ${http://host:port/v0}
```

{{% alert hint %}}
Note that the `--configurator` argument is required, you should pass
in Ohara configurator API URL.
{{% /alert %}}

You can also override the default port `5050` by passing in `--port`
like the following:

```shell script
$ yarn start --configurator ${http://host:port/v0} --port ${1234}
```

After starting the server, visit `http://localhost:${PORT}` in your browser.

{{% alert hint %}}
Passing the CLI option `-c` has the same effect as `--configurator`
and `-p` for `--port` as well
{{% /alert %}}

Double check the `--configurator` spelling and API URL, the URL should
contain the API version number: `/v0`

#### Client

Start the **Client** development server with:

```shell script
$ yarn start:client
```

After starting the dev server, visit `http://localhost:3000` in your
browser and start you development.

You can override the default port `3000` by passing in an environment
variable:

```shell script
$ PORT=7777 yarn start:client
```

The dev server will then start at `http://localhost:7777`

### Testing

#### Unit test

You can run **Client** unit test with a single npm script:

```shell script
$ yarn test:unit:ci
```

Please note that this is a one-off run command, often when you're in
the development, you would run test and stay in Jest's watch mode
which reloads the test once you save your changes:

```shell script
$ yarn test:unit:watch
```

```shell script
$ yarn test:unit:ci
```

This command will generate test coverage reports, which can be found in
`ohara-manager/client/coverage/ut/`

{{% alert note %}}
We will automate check the threshold of code coverage by
**Statements > 40%** if you issued the `test:unit:ci` command.
{{% /alert %}}

#### API test

We're using Cypress to test our RESTful API, this ensures our backend
API is always compatible with Ohara manager and won't break our UI
(ideally). You can run the test in different modes:

**GUI mode**: this will open Cypress test runner, you can then run
your test manually through the UI.

```shell script
$ yarn test:api:open
```

**Electron mode(headless)**: since we're running our API test on CI
under this mode. You might often want to run your tests in this mode
locally as well.

```shell script
$ yarn test:api:ci --configurator ${http://host:port/v0 --port 0}
```

> 1. Generated test coverage reports could be found in `ohara-manager/client/coverage/api`
if you executed test with **Electron mode**.
> 2. The `--port 0` means randomly choose a port for this test run.

{{% alert note %}}
We will automate check the threshold of code coverage by
**Statements > 80%** if you issued the `test:api:ci` command.
{{% /alert %}}

#### IT test

To test our UI flows, we use Cypress to test our UI flow with **fake** configurator. These 
tests focus on the behaviors of UI flow (by different operations from UI), so they should 
cover most of our UI logic.  You can run the test in different modes:
                            
**GUI mode**: this will open Cypress test runner, you can then run
your test manually through the UI.

```shell script
$ yarn test:it:open
```

**Electron mode(headless)**: since we're running our API test on CI
under this mode. You might often want to run your tests in this mode
locally as well.

```shell script
$ yarn test:it:ci --configurator ${http://host:port/v0 --port 0}
```

This command will generate test coverage reports, which can be found in
`ohara-manager/client/coverage/it/`

{{% alert note %}}
We will automate check the threshold of code coverage by
**Statements > 75%** if you issued the `test:it:ci` command.
{{% /alert %}}

#### End-to-End test

Just like API test, our End-to-End test also runs in two different
modes:

**GUI mode**: this will open Cypress test runner, you can then run
your test manually through the UI.

```shell script
$ yarn test:e2e:open
```

**Electron mode(headless)**: since we're running our E2E test on CI
under this mode. You might often want to run your tests in this mode
locally as well.

```shell script
$ yarn test:e2e:ci --configurator ${http://host:port/v0}
```

1. Before running in this mode we advise that you run `yarn setup` prior
   to the tests as the dev server is not running, so you might have stale
   build asserts in your build directory

2. You also need to create a **cypress.env.json** under the
   `/ohara-manager/client/`, these are the config that Cypress will
   be using when running tests:

```json
{
  "nodeHost": "ohara-dev-01",
  "nodePort": 22,
  "nodeUser": "nodeUserName",
  "nodePass": "nodePassword",
  "servicePrefix": "prPrefix"
}
```

3.  Unlike API test, the test should run in production environment

### Code Coverage

As the above test phases are tend to cover different range of our source code, after you executed 
the following commands:
- `yarn test:unit:ci`
- `yarn test:api:ci`
- `yarn test:it:ci`

we will generate the corresponded coverage reports in relative path as each test section described, and 
you could combine them to see the overall picture:

```shell script
$ yarn report:combined
```

The combined coverage report could be found at `/ohara-manager/client/coverage/index.html`

{{% alert info %}}
You could also check the combined report by yourself:
```shell script
$ yarn test:coverage:check
```
{{% /alert %}}

### Linting

We use [ESLint](https://github.com/eslint/eslint) to lint all the
JavaScript:

Server:

```shell script
$ yarn lint:server
```

It's usually helpful to run linting while developing and that's
included in `yarn start` command:

```shell script
$ yarn start --configurator ${http://host:port/v0}
```

This will start the server with `nodemon` and run the linting script
whenever nodemon reloads.

Client:

Since our client is bootstrapped with create-react-app, so the
linting part is already taken care. When starting the **Client** dev
server with `yarn start:client`, the linting will be starting
automatically.

Note that due to create-react-app doesn't support custom eslint
rules. You need to use your text editor plugin to display the custom
linting rule warnings or errors. For more info about this, please
take a look at the create-react-app
[docs](https://facebook.github.io/create-react-app/docs/setting-up-your-editor#displaying-lint-output-in-the-editor)

### Format

We use [Prettier](https://github.com/prettier/prettier) to format our
code. You can format all `.js` files with:

```shell script
$ yarn format
```

- You can ignore files or folders when running `yarn format` by
  editing the `.prettierignore` in the Ohara-manager root.

### Build

**Note that this step is only required for the Client NOT THE SERVER**

You can get production-ready static files by using the following
command:

```shell script
$ yarn build
```

{{% alert hint %}}
These static files will be built and put into the
**/ohara-manager/client/build** directory.
{{% /alert %}}

### Ohara manager image

Run the following command to get the production ready build of both
the **Server** and **Client**.

```shell script
$ yarn setup
```

After the build, copy/use these files and directories to the
destination directory (Note this step is automatically done by
Ohara-${module} module):

- start.js
- config.js
- client -- only build directory is needed
  - build
- constants
- node_modules
- routes
- utils


{{% alert note %}}
Note that if you add new files or dirs to the **Server** or **Client**
and these files and dirs are required for production build, please
list that file in the above list as well as editing the gradle file
under `ohara/ohara-manager/build.gradle`. **Skipping this step will
cause production build failed!**
{{% /alert %}}

**From the Ohara manager project root**, use the following command to
start the manager:

```shell script
$ yarn start:prod --configurator ${http://host:port/v0}
```

### CI server integration

In order to run tests on Jenkins, Ohara manager provides a few npm scripts that are used in Gradle, these scripts generate test reports in _/ohara-manager/test-reports_ which can be consumed by Jenkins to determine if a test passes or not, you will sometimes need to run these commands locally if you edit related npm scripts in Ohara manager or want to reproduce fail build on CI:

Unit test:

```shell script
$ ./gradlew test
```

- Run **Client**'s unit tests. The test reports can be found in `ohara-manager/test-reports/`

API test:

```shell script
$ ./gradlew api -Pohara.manager.api.configurator=${http://host:port/v0}
```

End-to-End test:

```shell script
$ ./gradlew e2e -Pohara.manager.e2e.port=5050 -Pohara.manager.e2e.configurator=${http://host:port/v0}-Pohara.manager.e2e.nodeHost=${slaveNodeName} -Pohara.manager.e2e.nodePort=${slaveNodePort} -Pohara.manager.e2e.nodeUser=${slaveNodeUsername} -Pohara.manager.e2e.nodePass=${slaveNodePassword} -Pohara.it.container.prefix=${pullRequestNumber}
```

Let's take a close look at these options:

- `-Pohara.manager.e2e.port=5050`: start Ohara manager at this port in the test run
- `-Pohara.manager.e2e.configurator=${http://host:port/v0}`: Configurator URL, Ohara manager will hit this API endpoint when running test
- `-Pohara.manager.e2e.nodeHost=${slaveNodeName}`: K8s' slave node, the services started in the test will be deploy on this node
- `-Pohara.manager.e2e.nodePort=${slaveNodePort}`: port of the given node
- `-Pohara.manager.e2e.nodeUser=${slaveNodeUsername}`: username of the given node
- `-Pohara.manager.e2e.nodePass=${slaveNodePassword}`: password of the given node
- `-Pohara.it.container.prefix=${pullRequestNumber}`: pull request number of this test run, this prefix is used by Jenkins to do the cleanup after the test is done

There are more gradle tasks that are not listed in the above, you can
view them in `/ohara-manager/build.gradle`

### Clean

Clean up all running processes, removing `test-reports/` in the
**Server** and `/build` directory in the **Client**:

```shell script
$ yarn clean
```

Clean all running processes started with node.js

```shell script
$ yarn clean:process
```

This is useful when you want to kill all node.js processes locally

### Prepush

We also provide a npm script to run Client's unit test, linting, and
format all the JavaScript files with. **Ideally, you'd run this
before pushing your code to the remote repo:**

```shell script
$ yarn prepush
```

## Editors

We highly recommended that you use [Visual Studio
Code](https://code.visualstudio.com/) (vscode for short) to edit and
author Ohara manager code.

Recommended vscode settings

```json
{
  "editor.tabSize": 2,
  "editor.formatOnSave": true,
  "editor.formatOnSaveTimeout": 2000,
  "editor.tabCompletion": true,
  "emmet.triggerExpansionOnTab": true,
  "emmet.includeLanguages": {
    "javascript": "javascriptreact",
    "markdown": "html"
  },
  "search.exclude": {
    "**/node_modules": true,
    "**/bower_components": true,
    "**/coverage": true
  },
  "javascript.updateImportsOnFileMove.enabled": "always",
  "eslint.workingDirectories": [
     "./client"
   ]
}
```

**Recommend extensions**

- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) --- 
  install this so vscode can display linting errors right in the editor

- [vscode-styled-components](https://marketplace.visualstudio.com/items?itemName=jpoissonnier.vscode-styled-components) --- 
  syntax highlighting support for [styled component](https://github.com/styled-components/styled-components)

- [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) ---
  code formatter, it consumes the config in `.prettierrc`

- [DotENV](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv) --- 
  `.env` file syntax highlighting support

- [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight) --- 
  Highlight web colors in VSCode

## Switch different version of Node.js

Oftentimes you would need to switch between different Node.js versions
for debugging. There's a handy npm package that can reduce the pain of
managing different version of Node.js on your machine:

First, let's install this package `n`, note that we're installing it globally so it's can be used throughout your projects:

```shell script
$ npm install -g n # or yarn global add n
```

Second, let's use `n` to install a specific version of Node.js:

```shell script
$ n 8.16.0
```

Switch between installed NodeJS versions:

```shell script
$ n # Yep, just type n in your terminal...,
```

For more info, you can read the [docs](https://github.com/tj/n) here.

## Having issues? {#having-issues}

- **Got an error while starting up the server: Error: Cannot find
  module ${module-name}**

  If you're running into this, it's probably that this module is not
  correctly installed on your machine. You can fix this by simply
  run:

  ```shell script
  $ yarn # If this doesn't work, try `yarn add ${module-name}`
  ```

  After the installation is completed, start the server again.

- **Got an error while starting up the server or client on a Linux
  machine: ENOSPC**

  You can run this command to increase the limit on the number of
  files Linux will watch. Read more
  [here](https://github.com/guard/listen/wiki/Increasing-the-amount-of-inotify-watchers#the-technical-details).

  ```shell script
  $ echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p.
  ```

- **Node.js processes cannot be stopped even after using kill -9**

  We're using `forever` to start our node.js servers on CI, and
  `nodemon` while in development, so you need to use the following
  commands to kill them. `kill -9` or `fuser` might not work as you
  expected.
  
  use `yarn clean:processes` command or `pkill node` to kill all the
  node.js processes

- **While running test in jest's watch modal, an error is thrown**

  ```
  Error watching file for changes: EMFILE
  ```

Try installing `watchman` for your mac with the [instruction](#mac)

For more info: <https://github.com/facebook/jest/issues/1767>

- **Ohara manager is not able to connect to Configurator** And I'm seeing something like:

  ```
  --configurator: we're not able to connect to `{http://host:port/v0}`
  Please make sure your Configurator is running at `${http://host:port/v0}`
  [nodemon] app crashed - waiting for file changes before starting...
  ```

This could happen due to several reasons:

- **Configurator hasn't fully started yet**: after you start the
  configurator container. The container needs some time to fully
  initialize the service. This usually takes about a minute or
  so. And as we're doing the API check by hitting the real API
  in Ohara manager. This results to the error in the above.

- **You're not using the correct IP in Manager container**: if
  you start a configurator container in your local as well as a
  manager. You should specify an IP instead of something like
  localhost in: `--configurator http://localhost:12345/v0` This
  won't work as the manager is started in the container so it
  won't be able to connect to the configurator without a real IP

- As we mentioned in the previous sections. Please double
  check your configurator URL spelling. This is usually the
  cause of the above-mentioned error.
