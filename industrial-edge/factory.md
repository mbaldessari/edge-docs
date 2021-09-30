---
layout: default
title: Factory Sites
grand_parent: Patterns
parent: Industrial Edge
nav_order: 1
---

# Having a factory (edge) cluster join the datacenter (hub) 
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

Rather than provide instructions on creating a factory cluster it is assumed
that an OpenShift cluster has already been created. Use the `openshift-install` program provided at [cloud.redhat.com](https://console.redhat.com/openshift/create "Create an OpenShift cluster")

There are a three ways to join the factory to the datacenter.

* Using the ACM user interface
* Using the `cm` tool
* Using the `clusteradm` tool

## Factory setup using the ACM UI

1. From the datacenter openshift console select ACM from the top right

![](docs/images/launch-acm-console.png "Launch ACM console")

2. Select the "Import cluster" option beside the highleded Create Cluster button.

![](docs/images/import-cluster.png "Select Import cluster")

3. On the "Import an existing cluster" page, enter the cluster name and choose Kubeconfig as the "import mode". After pressiming import you will be asked to copy a command that will be used on the factory cluster.

![](docs/images/import-with-kubeconfig.png "Import using kubeconfig")

Skip to the section [Factory is joined](#factory-is-joined)

## Factory setup using `cm` tool

1. Install the `cm` (cluster management) CLI tool. See details [here](https://github.com/open-cluster-management/cm-cli/#installation)

1. Obtain the KUBECONFIG file from the edge/factory cluster.

1. On the command line login into the hub/datacenter cluster (use `oc login` or export the KUBECONFIG).

1. Run the following command:
```
cm attach cluster --cluster <cluster-name> --cluster-kubeconfig <path-to-KUBECONFIG>
``` 

Skip to the section [Factory is joined](#factory-is-joined)

## Factory setup using `clusteradm` tool

You can also use `clusteradm` to join a cluster. The folloing instructions explain what needs to be done. `clusteradm` is still in testing.

1. To deploy a edge cluster you will need to get the datacenter (or hub) cluster's token. You will need to install `clusteradm`.  On the existing *datacenter cluster*:

   `clusteradm get token`

1. When you run the `clusteradm` command above it replies with the token and also shows you the command to use on the factory. So first you must login to the factory cluster

   `oc login`
   or
   
   `export KUBECONFIG=~/my-ocp-env/factory`

1. Then request to that the factory join the datacenter hub

   `clusteradm join --hub-token <token from clusteradm get token command > <factory cluster name>`

1. Back on the hub cluster accept the join reguest 

   `clusteradm accept --clusters <factory-cluster-name>`

Skip to the next section, [Factory is joined](#factory-is-joined)

## Factory is joined
That's it! Go to your factory (edge) OpenShift console and check for the open-cluster-management-agent pod being launched. Be patient, it will take a while for the ACm agent and agent-addons to launch. After that, the operator OpenShifdt GitOps will run. When it's finished coming up launch the OpenShift GitOps (ArgoCD) console from the top right of the OpenShift console. 