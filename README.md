# chat.vonx

**A Fully Functional Work In Progress**

This project consists of a set of OpenShift templates and settings to deploy a Rocket.Chat instance, complete with persistent storage into an OpenShift environment.

The work is based on the templates from [Rocket.Chat](https://github.com/RocketChat/Rocket.Chat).

## Improvements
- The templates have been separated into their component parts; Rocket.Chat and MongoDB.
- Image Streams for Rocket.Chat and MongoDB have been separated from the deployment configurations, and placed into build configurations.
- Upgraded to MongoDB 3.6
- Database credentials are stored in secrets.
- Templates allow full control over memory and CPU resources.  In other words they are not hard coded or relying on defaults.

## Deployment - Quick Start

### Prerequisites:

This project uses the scripts found in [openshift-developer-tools](https://github.com/BCDevOps/openshift-developer-tools) to setup and maintain OpenShift environments.  Refer to the [OpenShift Scripts](https://github.com/BCDevOps/openshift-developer-tools/blob/master/bin/README.md) documentation for details.

These instructions assume:
- You have, AT MINIMUM, Git, and the OpenShift CLI installed on your system, and they are functioning correctly.  If you are working locally you will also need Docker.  The recommended approach is to use either [Homebrew](https://brew.sh/) (MAC) or [Chocolatey](https://chocolatey.org/) (Windows) to install the required packages.
- You have followed the [OpenShift Scripts](https://github.com/BCDevOps/openshift-developer-tools/blob/master/bin/README.md) environment setup instructions to install and configure the scripts for use on your system.
- You have forked and cloned a local working copy of the project source code.
- You are using a reasonable shell.  A "reasonable shell" is obvious on Linux and Mac, and is assumed to be the git-bash shell on Windows.
- You are working from the top level `./openshift` directory for the project.
- You have an available OpenShift project set and you have administrative privileges to that project set.

For the commands mentioned in these instructions, you can use the `-h` parameter for usage help and options information.

### Update the settings file

Update the [setting.sh](./openshift/settings.sh) file to point to your OpenShift project set.

Update:
```
export PROJECT_NAMESPACE="devex-von"
```

To reference your project's namespace, leaving off the `-tools`, `-dev`, `-test`, `-prod` qualifiers:
```
export PROJECT_NAMESPACE="myteam-myproject"
```

### Create the build configurations

The following instructions will create the build configurations, containing the Rocket.Chat and MongoDB image streams, in your `tools` project.

From the `./openshift` directory run;
```
Wade@hvWin10x64 MINGW64 /c/chat.vonx/openshift (master)
$ genBuilds.sh

Loading settings ...
Loading settings from /c/chat.vonx/openshift/settings.sh ...

Configuring the devex-von-tools environment for . ...

Switching to devex-von-tools ...

...
```

### Create the deployment configurations

The following instructions will create the deployment configurations for Rocket.Chat and MongoDB in your selected deployment project (the example uses `prod`).

From the `./openshift` directory run;
```
Wade@hvWin10x64 MINGW64 /c/chat.vonx/openshift (master)
$ genDepls.sh -e prod

Loading settings ...
Loading settings from /c/chat.vonx/openshift/settings.sh ...

Configuring the prod environment for . ...

Switching to devex-von-prod ...
Removing dangling configuration files ...

Generating deployment configuration files ...

Processing deployment configuration; templates/MongoDB/mongodb-deploy.json ...
Loading parameter overrides for templates/MongoDB/mongodb-deploy.json ...

Initializing mongodb-deploy.overrides ...

Creating a set of random database credentials ...

...
```

### Deploy the images

The following instructions will tag the Rocket.Chat and MongoDB images for deployment (the example uses `prod`).  *Yes, I know, this can be scripted, but I did say it's a WIP.*

Deploy MongoDB (example, change the namespace to reference your `tools` project)
```
Wade@hvWin10x64 MINGW64 /c/chat.vonx/openshift (master)
$ oc tag mongodb:latest mongodb:prod -n devex-von-tools
```

Monitor the deployment and wait for the MongoDB container to fully startup before continuing.

Deploy Rocket.Chat (example, change the namespace to reference your `tools` project)
```
Wade@hvWin10x64 MINGW64 /c/chat.vonx/openshift (master)
$ oc tag rocketchat:latest rocketchat:prod -n devex-von-tools
```

Monitor the deployment and wait for the rocketchat container to fully startup.

### Login - Create Admin Account, Configure Instance

*The route for the rocketchat configuration it currently auto generated (that's going to change).  If you wish, create a new route that suits your needs and delete the old one before you continue.*

Browse to the route defined for the rocketchat configuration.

Login, creating the admin account for the instance.

Follow along with the Setup Wizard

Finish off by reviewing and updating other site settings, such as configuring email.

## References:

### Documentation:

*At the time of writing (2018.10.17) I found the Rocket.Chat documentation to be a little sparse and it does not reflect the current version completely.*

- [Rocket.Chat Documentation](https://rocket.chat/docs/)
- [Rocket.Chat Administrator Guides](https://rocket.chat/docs/administrator-guides/)

### Documentation - Deploying Rocket.Chat on OpenShift

Various articles on how to deploy Rocket.Chat into OpenShift which are just historical reference material now.  For the most part you can ignore these articles.  The templates and scripts for this project handle all the details for you.

*You don't need to disable the domain check for email as described in the articles.*

- https://rocket.chat/docs/installation/paas-deployments/openshift/#deploying-rocketchat-on-openshift
- https://rocket.chat/docs/installation/automation-tools/openshift/
- http://www.opensourcearchitects.org/index.php/2017/08/31/deploying-a-rocketchat-server-on-openshift/

### MongoDB image:
- https://access.redhat.com/containers/#/registry.access.redhat.com/rhscl/mongodb-36-rhel7

### Rocket.Chat images
- (Using this one) https://hub.docker.com/r/rocketchat/rocket.chat/
- https://hub.docker.com/_/rocket.chat/
- https://access.redhat.com/containers/#/registry.connect.redhat.com/rocketchat/rocketchat

### Reference templates:
- (Used these because they were updated more recently) https://github.com/RocketChat/Rocket.Chat/tree/develop/.openshift
- https://github.com/rimolive/rocketchat-openshift

## Getting Help or Reporting an Issue
To report bugs/issues/feature requests, please file an [issue](../../issues).

## How to Contribute
If you would like to contribute, please see our [CONTRIBUTING](./CONTRIBUTING.md) guidelines.

Please note that this project is released with a [Contributor Code of Conduct](./CODE_OF_CONDUCT.md). 
By participating in this project you agree to abide by its terms.
