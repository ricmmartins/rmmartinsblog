---
title: "Deploying an application on OpenShift Local: A Beginner's Guide"
date: 2023-12-08T11:00:00-00:00
tags:
  - openshift
---

## Introduction

OpenShift, the Red Hat's application platform, offers a robust environment for deploying and managing containerized applications. 

Using a local OpenShift environment, offers several benefits, especially for developers who are new to OpenShift or Kubernetes:

- Safe Learning Environment: It allows experimenting without the risk of affecting a production environment. This is crucial for beginners who are learning the ropes of container orchestration and application deployment.
- Cost-Effective: There's no need for cloud resources, making it an economical solution for testing and development purposes.
- Convenience: Developers can easily test and debug their applications locally, which streamlines the development process.

Several methods for setting up OpenShift locally include:

1. **[OpenShift Local](https://developers.redhat.com/products/openshift-local/overview)**: This is the new name for CodeReady Containers. OpenShift Local is an official Red Hat solution to run OpenShift 4.x locally. It provides a straightforward way to create a single-node OpenShift 4 cluster.
2. **[MiniShift](https://github.com/minishift/minishift)**: An older tool compared to CodeReady Containers. MiniShift was commonly used for running a single-node OpenShift cluster. It runs on top of a virtual machine and is suitable for development and testing purposes. MiniShift supports OpenShift 3.x versions.
3. **[OKD](https://www.okd.io/)**: OKD is the Community Distribution of Kubernetes that powers Red Hat OpenShift. It offers more flexibility and can be used to set up a more extensive development environment than CodeReady Containers or MiniShift. However, setting up an OKD cluster is generally more complex.
4. **Containerized Development Environments**: Some developers choose to use containerized development environments that mimic OpenShift's behavior. Tools like [Docker](https://www.docker.com/) and [Podman](https://podman.io/) can be used to run OpenShift components in containers. This approach requires more manual setup and configuration.

Each method has its benefits, depending on your project needs and system capabilities. In this post I'll cover the usage of OpenShift Local.

## Step-by-Step Deployment on Local OpenShift

To get started with OpenShift Local, download the crc tool from the [Red Hat Console](https://console.redhat.com/openshift/create/local). If you don't have a Red Hat account, you can create one for free with the [Red Hat Developer program](https://developers.redhat.com/about).

### Step 1: Start Your Local OpenShift Cluster

1. Download OpenShift Local: Visit the [OpenShift Local download page](https://cloud.redhat.com/openshift/install/crc/installer-provisioned) and download the version for your OS.
2. Install CodeReady Containers:
- Extract the downloaded file.
- Run the setup command: `crc setup`.
3. Start the OpenShift Cluster:
- Initialize the cluster: `crc start`.
- This process may take several minutes.
4. Access OpenShift Console:
Retrieve the console URL and login details: `crc console --credentials`.

### Step 2: Install the OpenShift CLI (oc)

If you haven't already installed the OpenShift CLI, download and install it from the [Red Hat Console](https://console.redhat.com/openshift/downloads#tool-oc).

### Step 3: Authenticate to OpenShift

Authenticate to your OpenShift cluster using the `oc` CLI,  this will allow you to execute deployment commands:

```bash
oc login -u developer -p developer
```

### Step 4: Create a New Project

In OpenShift, a project is a Kubernetes namespace with additional annotations. It's a way to organize and isolate your resources and work.  It's a way to group resources in a logical manner.

```bash
oc new-project my-php-project
```

### Step 5: Deploy the Application

In this guide, we'll deploy the [ricmmartins/aro-demo-dryrun](https://github.com/ricmmartins/aro-demo-dryrun) PHP application. 

When you use the `oc new-app` command with a remote repository URL, OpenShift directly pulls the code from the remote repository for deployment. This is a straightforward method for deployment without requiring a local copy of the code. It's efficient when you don't need to modify the code and want to deploy it as-is.

This command analyzes the repository and creates appropriate OpenShift resources (like BuildConfig, DeploymentConfig, Service) for the application.

```bash
oc new-app https://github.com/ricmmartins/aro-demo-dryrun.git
```

### Step 6: Monitor the Deployment

To monitor the deployment process, use:

```bash
oc status
```
This command provides real-time status updates about the deployment process, helping you identify and troubleshoot any issues.

### Step 7: Expose Your Application

After the deployment, expose your application to make it accessible outside the OpenShift cluster. 

```bash
oc expose svc/aro-demo-dryrun
```

### Step 8: Access the Application

Use `oc get route` to find the URL of your application:

```bash
oc get route/aro-demo-dryrun
```

Visit the URL in your browser to view your PHP application.

### Conclusion

Deploying applications on OpenShift doesn't have to be complicated. Using the oc CLI, you can efficiently deploy and manage applications directly from source code repositories like GitHub. This approach offers a powerful and flexible way to work with OpenShift, making it an excellent tool for developers at all levels.
