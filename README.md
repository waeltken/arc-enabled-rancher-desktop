# Azure Arc Enabled Rancher Desktop

This tutorial show you how to enable Azure Arc on your Desktop using Rancher
Desktop and the Azure CLI.

> Rancher is an open-source desktop application for Mac, Windows and Linux.
> 
> Rancher Desktop runs Kubernetes and container management on your desktop. You can choose the version of Kubernetes you want to run. You can build, push, pull, and run container images using either containerd or Moby (dockerd). The container images you build can be run by Kubernetes immediately without the need for a registry.

Get Rancher Desktop from their [official website](https://rancherdesktop.io/).

> With Azure Arc-enabled Kubernetes, you can attach and configure Kubernetes clusters running anywhere. You can connect your clusters running on other public cloud providers (GCP, AWS) or clusters running on your on-premise data center (on VMware vSphere, Azure Stack HCI) to Azure Arc. When you connect a Kubernetes cluster to Azure Arc, it will:
> 
>  * Get an Azure Resource Manager representation with a unique ID.
>  * Be placed in an Azure subscription and resource group.
>  * Receive tags just like any other Azure resource.

When installing make sure to select the dockerd (moby) container runtime. You'll
also want to make your Rancher installation available to your WSL distros on
your Desktop. With the Rancher Desktop App running (I've found that closing the
Windows also stops the cluster) we run a few Azure CLI commands to onboard or
local cluster to Azure Arc.

We'll be following the [official quickstart documentation](https://docs.microsoft.com/azure/azure-arc/kubernetes/quickstart-connect-cluster)


Register the Kubernetes Arc provider in your Azure subscription, so with you cli
logged into the right subscription:

```shell
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ExtendedLocation
```

Show the status of the provider registration like this and wait for it to become
ready.

```shell
az provider show -n Microsoft.Kubernetes
az provider show -n Microsoft.KubernetesConfiguration
az provider show -n Microsoft.ExtendedLocation
```

Create resource group for you Arc Enabled Cluster to show up in:

```shell
az group create --name AzureArcDesktop --location WestEurope
```

And then with you local Rancher Desktop cluster in your kubectl context you run the `az connectedk8s connect` command. You can check the current context using kubectl:

```shell
$ kubectl config current-context
rancher-desktop # output of kubectl config
```

```shell
az connectedk8s connect --name AzureArcDesktop1 --resource-group AzureArcDesktop
```

This command will install the Azure Arc operators on your cluster in the
`azure-arc` namespace. It might take up to 15 minutes for your cluster to become live and visible as connected on the Azure Portal.

## Take it further
