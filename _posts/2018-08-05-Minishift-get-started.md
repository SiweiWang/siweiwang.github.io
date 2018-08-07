---
layout: post
title:  "Getting started with Minishift"
date:   2018-08-05 09:40:49 -0500
categories: minishift
tags: DevOps minishift
---

## Install minishift on Mac

***

### Setting Up xhyve Driver

xhyve hypervisor is used by minishift for MacOS. Following is the setps on how to install xhyve driver on MacOS

Use the following command to install the latest version of the driver with Homebrew:


`$ brew install docker-machine-driver-xhyve`

Once installed, enable root access for the docker-machine-driver-xhyve binary and add it to the default wheel group:

`$ sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve`
Set the owner User ID (SUID) for the binary as follows:

`$ sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve`

Verify that xhyve driver is installed successfully

`brew info --installed docker-machine-driver-xhyve`

***

### Install minishift
After the xhyve driver is installed and verified, install minishift

`$ brew cask install minishift`

### Starting Minishift
After minishift is installed, start minishift

` minishift start`

If minishift starts successfully, you will see output similar to the follow:

```
Using 192.168.64.2 as the server IP
Starting OpenShift using openshift/origin:v3.9.0 ...
OpenShift server started.

The server is accessible via web console at:
    https://192.168.64.2:8443
```

Note that the ip is random choose and you can get the ip address of the vm using:

`minishift ip`

After minishift started, you can access the web console at:

https://<minishift_ip_address>:8443. In above case, it would be https://192.168.64.2:8443.

The default username `developer` and the default password is `developer`

You can also login to the openshift command line:

`oc login https://<minishift_ip_address>:8443/  -u developer -p developer`

You can create an example app using:

`oc new-app https://github.com/openshift/nodejs-ex -l name=myapp`

You can track the log using:

`oc logs -f bc/nodejs-ex`
 
After the build is finished, a new service should get started. You can list the service using

`oc get service`

This service is not exposed yet, you can expose the service using:

`oc expose svc/nodejs-ex`

this command will create a new route in openshift to allow external traffic to the service. You get verify the route got created using

`oc get routes`

HOST is where the host where the service is exposed at. 

For example

```
$ oc get routes  
NAME        HOST/PORT                                 PATH      SERVICES    PORT       TERMINATION   WILDCARD
nodejs-ex   nodejs-ex-myproject.192.168.64.2.nip.io             nodejs-ex   8080-tcp                 None
```

In this case, nodejs-ex-myproject.192.168.64.2.nip.io is the endpoint the service is exposed.


Reference:
[okd documentation](https://docs.okd.io/latest/minishift/getting-started/quickstart.html#starting-minishift)