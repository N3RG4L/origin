OpenShift Application Platform
==============================

[![GoDoc](https://godoc.org/github.com/openshift/origin?status.png)](https://godoc.org/github.com/openshift/origin)
[![Travis](https://travis-ci.org/openshift/origin.svg?branch=master)](https://travis-ci.org/openshift/origin)

This is the source repository for [OpenShift 3](https://openshift.github.io), based on top of [Docker](https://www.docker.io) containers and the
[Kubernetes](https://github.com/GoogleCloudPlatform/kubernetes) container cluster manager.
OpenShift adds developer and operational centric tools on top of Kubernetes to enable rapid application development,
easy deployment and scaling, and long-term lifecycle maintenance for small and large teams and applications.

**Features:**

* Push source code to the platform and have deployments automatically occur
* Easy to use client tools for building web applications from source code
  * Templatize the components of your system, reuse them, and iteratively deploy them over time
* Centralized administration and management of application component libraries
  * Roll out changes to software stacks to your entire organization in a controlled fashion
* Team and user isolation of containers, builds, and network communication in an easy multi-tenancy system
  * Limit, track, and manage the resources teams are using

**Learn More:**

* **[Technical Architecture Presentation](https://docs.google.com/presentation/d/1Isp5UeQZTo3gh6e59FMYmMs_V9QIQeBelmbyHIJ1H_g/pub?start=false&loop=false&delayms=3000)**
* **[System Architecture](https://github.com/openshift/openshift-pep/blob/master/openshift-pep-013-openshift-3.md)** design document
* The **[Trello Roadmap](https://ci.openshift.redhat.com/roadmap_overview.html)** covers the epics and stories being worked on (click through to individual items)
* **[Public Documentation](http://docs.openshift.org/latest/welcome/index.html)** site

For questions or feedback, reach us on [IRC on #openshift-dev](https://botbot.me/freenode/openshift-dev/) on Freenode or post to our [mailing list](https://lists.openshift.redhat.com/openshiftmm/listinfo/dev).

NOTE: OpenShift release candidate 1 is available on the [releases page](https://github.com/openshift/origin/releases). Feedback, suggestions, and testing are all welcome!


Security Warning!!!
-------------------
OpenShift is a system that runs Docker containers on your machine.  In some cases (build operations and the registry service) it does so using privileged containers.  Those containers access your host's Docker daemon and perform `docker build` and `docker push` operations.  As such, you should be aware of the inherent security risks associated with performing `docker run` operations on arbitrary images as they have effective root access.  This is particularly relevant when running the OpenShift as a node directly on your laptop or primary workstation.  Only run code you trust.

For more information on the security of containers, see these articles:

* http://opensource.com/business/14/7/docker-security-selinux
* https://docs.docker.com/articles/security/

Running untrusted containers will become less scary as improvements are made upstream to Docker and Kubernetes, but until then please be conscious of the images you run.  Consider using images from trusted parties, building them yourself on OpenShift, or only running containers that run as non-root users.


Docker 1.6
----------
OpenShift now requires at least Docker 1.6. Here's how to get it:

### Fedora 21
RPMs for Docker 1.6 are available for Fedora 21 in the updates yum repository.

### CentOS 7
RPMs for Docker 1.6 are available for CentOS 7 in the extras yum repository.


Getting Started
---------------
The simplest way to run OpenShift Origin is in a Docker container:

    $ sudo docker run -d --name "openshift-origin" --net=host --privileged \
        -v /var/run/docker.sock:/var/run/docker.sock \
        openshift/origin start

Once the container is started, you can jump into a console inside the container and run the CLI.

    $ sudo docker exec -it openshift-origin bash
    $ oc --help
    $ oc login
    Username: test
    Password: test
    $ oc new-project test
    $ oc new-app -f https://raw.githubusercontent.com/openshift/origin/master/examples/sample-app/application-template-stibuild.json

Any username and password are accepted by default (with no credential system configured).  You can view the webconsole at https://localhost:8443/ in your browser - login with the same credentials you used above and you'll see the application you just created.

![Web console overview](docs/screenshots/console_overview.png?raw=true)


### Next Steps

We highly recommend trying out the [OpenShift walkthrough](https://github.com/openshift/origin/blob/master/examples/sample-app/README.md), which shows some of the lower level pieces of of OpenShift that will be the foundation for user applications.  The walkthrough is accompanied by a blog series on [blog.openshift.com](https://blog.openshift.com/openshift-v3-deep-dive-docker-kubernetes/) that goes into more detail.  It's a great place to start, albeit at a lower level than OpenShift 2.

Both OpenShift and Kubernetes have a strong focus on documentation - see the following for more information about them:

* [OpenShift Documentation](http://docs.openshift.org/latest/welcome/index.html)
* [Kubernetes Getting Started](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/README.md)
* [Kubernetes Documentation](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/README.md)


### Troubleshooting

If you run into difficulties running OpenShift, start by reading through the [troubleshooting guide](https://github.com/openshift/origin/blob/master/docs/debugging-openshift.md).


API
---

The OpenShift APIs are exposed at `https://localhost:8443/oapi/v1/*`.

To experiment with the API, you can get a token to act as a user:

    $ sudo docker exec -it openshift-origin bash
    $ oc login
    Username: test
    Password: test
    $ oc whoami -t
    <prints a token>
    $ exit
    # from your host
    $ curl -H "Authorization: bearer <token>" https://localhost:8443/oapi/v1/...


### API Documentation

The API documentation can be found [here](http://docs.openshift.org/latest/rest_api/openshift_v1.html).


FAQ
---

1. How does OpenShift relate to Kubernetes?

    OpenShift embeds Kubernetes and adds additional functionality to offer a simple, powerful, and
    easy-to-approach developer and operator experience for building applications in containers.
    Kubernetes today is focused around composing containerized applications - OpenShift adds
    building images, managing them, and integrating them into deployment flows.  Our goal is to do
    most of that work upstream, with integration and final packaging occurring in OpenShift.  As we
    iterate through the next few months, you'll see this repository focus more on integration and
    plugins, with more and more features becoming part of Kubernetes.

2. What can I run on OpenShift?

    OpenShift is designed to run any existing Docker images.  In addition you can define builds that will produce new Docker images from a Dockerfile.  However the real magic of OpenShift can be seen when using [Source-To-Image](https://github.com/openshift/source-to-image) builds which allow you to simply supply an application source repository which will be combined with an existing Source-To-Image enabled Docker image to produce a new runnable image that runs your application.  We are continuing to grow the ecosystem of Source-To-Image enabled images and documenting them [here](http://docs.openshift.org/latest/using_images/sti_images/overview.html). Our available images are:

    * [Ruby](https://github.com/openshift/sti-ruby)
    * [Python](https://github.com/openshift/sti-python)
    * [NodeJS](https://github.com/openshift/sti-nodejs)
    * [PHP](https://github.com/openshift/sti-php)
    * [Perl](https://github.com/openshift/sti-perl)
    * [Wildfly](https://github.com/openshift/wildfly-8-centos)

    Your application image can be easily extended with a database service with our [database images](http://docs.openshift.org/latest/using_images/db_images/overview.html). Our available database images are:

    * [MySQL](https://github.com/openshift/mysql)
    * [MongoDB](https://github.com/openshift/mongodb)
    * [PostgreSQL](https://github.com/openshift/postgresql)

Contributing
------------

You can develop [locally on your host](CONTRIBUTING.adoc#develop-locally-on-your-host) or with a [virtual machine](CONTRIBUTING.adoc#develop-on-virtual-machine-using-vagrant), or if you want to just try out OpenShift [download the latest Linux server, or Windows and Mac OS X client pre-built binaries](CONTRIBUTING.adoc#download-from-github).

First, **get up and running with the** [**Contributing Guide**](CONTRIBUTING.adoc).

All contributions are welcome - OpenShift uses the Apache 2 license and does not require any contributor agreement to submit patches.  Please open issues for any bugs or problems you encounter, ask questions on the OpenShift IRC channel (#openshift-dev on freenode), or get involved in the [Kubernetes project](https://github.com/GoogleCloudPlatform/kubernetes) at the container runtime layer.

See [HACKING.md](https://github.com/openshift/origin/blob/master/HACKING.md) for more details on developing on OpenShift including how different tests are setup.

If you want to run the test suite, make sure you have your environment set up, and from the `origin` directory run:

```
# run the unit tests
$ make check

# run a simple server integration test
$ hack/test-cmd.sh

# run the integration server test suite
$ hack/test-integration.sh

# run the end-to-end test suite
$ hack/test-end-to-end.sh

# run all of the tests above
$ make test
```

You'll need [etcd](https://github.com/coreos/etcd) installed and on your path for the integration and end-to-end tests to run, and Docker must be installed to run the end-to-end tests.  To install etcd you should be able to run:

```
$ hack/install-etcd.sh
```

Some of the components of OpenShift run as Docker images, including the builders and deployment tools in `images/builder/docker/*` and 'images/deploy/*`.  To build them locally run

```
$ hack/build-images.sh
```

To hack on the web console, check out the [assets/README.md](assets/README.md) file for instructions on testing the console and building your changes.


License
-------

OpenShift is licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/).
