Intel(r) IoT Services Orchestration Layer
====================================================
## Introduction

This is a solution that provides visual graphical programming for developing IoT applications.

The solution contains a HTML5 IDE running inside browser / WebView for developers to create the IoT application, including its internal logic (e.g. workflows) and its HTML5 based end user interface, through drag-and-drop in an easy and intuitiveway.

The solution also contains a distributed middleware running on top of Node.js to host and execute the IoT applications created by the IDE.

The middleware contains an Orchestration Center which runs the workflow engine to execute the logic, and web servers to host the HTML5 IDE for developers and HTML5 UI for end users. 

The middleware also contains one or multiple Service Hubs which actually manages the devices and cloud services in various procotols. The Service Hub roll up these information about services to Orchestration Center to let the developers create applications based on these services. The Service Hub also receives commands from Orchestration Center to actually invoke the services managed by it, according to the logic defined by workflows.

To understand this by a demo, please go through the instructions below and we do have a demo (with simulated devices and services) project as well.


## Important Notes

### Development Project and Release Project

As you would see below, to run this solution it needs to execute `./build.sh` and which takes time and is error-prone. To make the installation easier for end users, we also have another github project to host the ready-to-run project (i.e. outputs of running `./build.sh`) there, so end users no need to build by themselves.

This Release Project is  is located at https://github.com/01org/intel-iot-services-orchestration-layer 

### Proxies

As this is a solution based on Node.js, so the installation may touch npm (Node.js Package Manager) which would download and install dependant packages from internet.

So if you are behind a firewall / proxy, you may need to configure the proxy settings of your npm so you could download and install. You may do this by run `npm config edit` to open npm's configuration file, and configure related proxy items inside it. For example, add these lines in it

```
proxy=http://my_proxy:proxy_port
https-proxy=http://my_proxy:proxy_port
```

Please replace the `my_proxy` and `proxy_port` in above example accordingly.


## How to Start for Framework Developers

These are steps to setup on Linux to start contributing to the framework. It's workable on Windows under cygwin/mingw(e.g. gitbash) environment.

### Node.js

Node.js with version >= 0.12 is required. And you might need to configure npm proxy settings as mentioned above.

### Projects

The framework consists of multiple projects:

* base - Basic helper libraries including log etc.
* store - Abstraction layer for data. It provides the same abstracted API for databases, in memory hash tables, remote data providers etc.
* entity-store - Stores for various basic data used by the framework, for example, hub, thing, service, app, etc.
* entity - Higher level APIs upon entity-store, in particular, it provides application level lock to maintain certain data consistency between multiple stores.
* http-broker - A broker using HTTP protocol
* message - Abstraction layer for message infrastructures. It provides the same abstracted message APIs (send, pub/sub) built upon node.js event, mqtt, http broker, or any other actual implementations.
* session-manager - It handles the invokation of services. The consumption of services are organized as sessions.
* workflow - Workflow Engine. 
* hub - Major logic of creating and hosting a Hub
* center - Major logic of creating and hosting an Orchestration Center
* hub-center-shared - Some shared logic of hub and center
* ui-dev - Frontend code (running inside browser) for application developers
* ui-user - Frontend code (running inside browser) for end users
* ui-widgets - UI widgets for ui-dev and ui-user
* demo - Samples of configurations, services etc. for center and hub
* build - scripts to build the distributed version

Some of these projects may reference (i.e. require) others, so npm link is needed during the development. There is `link-npms.sh` to help build the dependencies.

### Start to Develop the Framework

As mentioned, Node.js >=0.12 need to be installed first. After that, run following commands to setup the environment. 

*NOTE* these start with # are comments and don't need to type in command shell. And you might need sudo to execute this depends on your setup of Node.js

*NOTE* As the setup and the following build stage heavily uses NPM, it should be connected to internet and do please configure NPM correctly if you are behind a proxy. You may use `npm config edit` and then configure the related proxy items.

*NOTE* To build optional doc system, we need to install pandoc >= v1.16.0.2 at first, see [Pandoc](http://pandoc.org/installing.html) for details.

```bash
    # If your Node.js isn't installed by NVM, you might need sudo to run this

    # Install related tools such as gulp, babel etc.
    ./dev-install.sh

    # npm link the projects to setup the dependencies
    ./link-npms.sh
```

Development of each projects are basically same as normal node.js projects. However, the ui-widgets,`ui-dev` and `ui-user` are frontend code so need to manually run `gulp build` under each's folder each time upon change, or run `gulp watch` to automatically build upon change.


### Build

To build, simply run

```bash
    ./build.sh nodoc auto_install
```

All necessary stuff would be built into `./dist` which could be immediately used anywhere.

If you need documentation system built as well, pleas remove the `nodoc` option.

By default, this script would clean the cached npm packages so every npm package would be downloaded again. If you hope to reuse the packages already downloaded, please add the option `noclean`

### Run Demo Project

To help understand the framework, a demo project is created. There are two ways to run the demo project. 

The first one does NOT need to build first. You may simiply run following commands after the environment is ready (e.g. have `npm install` etc.)

```bash
    # Start a sample HTTP broker at background
    ./run broker&
    # wait some seconds, run this to create an 
    # orchestration center at background
    ./run center&
    # wait some seconds, run this to create an 
    # hub at background with multiple simulated 
    # services / devicesat inside it to play with
    ./run hub&
```

After that, you may navigate to `http://localhost:8080` in browser for HTML5 UI for application developers, and `http://localhost:3000` for HTML5 UI for end users. You may replace localhost to real ip/host if you need to remotely connect to it.

Another way is to start the demo contained in the distribution built by `./build.sh`. Simple changed directory to `./dist` and run `./start_mock_demo.sh` to start the demo project.

To shutdown the demo, you need run `killall node` to kill all `Node.js` processes we started by `./run` or `./start_mock_demo.sh`.

## Convention

* Functions that serve for Constructor should start with the captial letter with Camel convention, e.g. `MyClass`
* Functions that are pure function would be small cases concated with underscore, e.g. `my_funtion`
* Functions that are async should return a promise, and the name should end with $. e.g. `my_function$`
* Indent with 2 spaces, no TAB
* Each line can only have 80 characters at most

## Documentation

The solution comes with a documentation system as well. The documentation are created in MD format and could be generated into HTML files by running `./gen_doc.sh` in `doc` subfolder, if `pandoc` has been installed already. 

After it is built, one may run `./dev-doc.sh` to view the documentations in browser. 

There are other ways to read the documentation in browser:

* After entire project is built (without `nodoc` option), one may enter `./dist` and run command `./start_doc.sh`
* Or in the HTML5 IDE of this solution, click the link `Help` in the top right corner.
* (Under Construction...) Read the [wiki of this project] (https://github.com/01org/intel-iot-services-orchestration-layer/wiki)