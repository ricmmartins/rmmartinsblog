---
title: "Deploying an application on OpenShift Local: A Beginner's Guide"
date: 2023-12-08T11:00:00-00:00
tags:
  - openshift
---

## Introduction

## Introduction

OpenShift, developed by Red Hat, extends Kubernetes to provide a more robust platform for deploying and managing containerized applications in a complete application platform. It integrates the core features of Kubernetes with additional tools and services to enhance developer productivity and operational efficiency. This guide aims to introduce beginners to deploying applications on OpenShift Local, a streamlined method to run OpenShift clusters locally for development and testing.

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

- Download OpenShift Local: Visit the [OpenShift Local download page](https://cloud.redhat.com/openshift/install/crc/installer-provisioned) and download the version for your OS.
- Install CodeReady Containers:
  - Extract the downloaded file.
  - Run the setup command: `crc setup`.
- Start the OpenShift Cluster:
  - Initialize the cluster: `crc start`.
  - This process may take several minutes.
- Access OpenShift Console:
  - Retrieve the console URL and login details: `crc console --credentials`.

 Expected output:

 ```bash
rmmartins@jarvis ~ сгс console --credentials
To login as a regular user, run 'oc login -u developer -p developer https://api.crc. testing:6443'.
To login as an admin,
login -u kubeadmin -p PNRGf-HT4jt-tyWvb-RdHqd https://api.crc.testing:6443'
```

![](/assets/images/openshiftlocal.png)

### Step 2: Install the OpenShift CLI (oc)

If you haven't already installed the OpenShift CLI, download and install it from the [Red Hat Console](https://console.redhat.com/openshift/downloads#tool-oc).

### Step 3: Authenticate to OpenShift

Authenticate to your OpenShift cluster using the `oc` CLI,  this will allow you to execute deployment commands:

```bash
oc login -u developer -p developer
```

### Step 4: Create a New Project

A project in OpenShift is akin to a Kubernetes namespace but with additional management features. It's a logical grouping that helps in resource organization, isolation, and multi-tenancy. In OpenShift, a project adds an extra layer of security and user management, allowing for more granular control over who can access what resources.

```bash
oc new-project my-php-project
```

Expected output:

```bash
rmmartins@jarvis ~ oc new-project my-php-project
Now using project "my-php-project" on server "https://api.crc.testing:6443".
You can add applications to this project with the 'new-app' command. For example, try:
oc new-app rails-postgresql-example
to build a new example application in Ruby. Or use kubectl to deploy a simple Kubernetes application:
kubectl create deployment hello-node --image=k8s.gcr.io/e2e-test-images/agnhost:2.33 -- /agnhost serve-hostname
```

This command creates a new project where all the subsequent resources related to the PHP application will be organized.

### Step 5: Deploy the Application

In this guide, we'll deploy the [ricmmartins/aro-demo-dryrun](https://github.com/ricmmartins/aro-demo-dryrun) PHP application. 

When you use the `oc new-app` command with a remote repository URL, OpenShift directly pulls the code from the remote repository for deployment. This is a straightforward method for deployment without requiring a local copy of the code. It's efficient when you don't need to modify the code and want to deploy it as-is.

This command analyzes the repository and creates appropriate OpenShift resources (like BuildConfig, DeploymentConfig, Service) for the application.

```bash
oc new-app https://github.com/ricmmartins/aro-demo-dryrun.git
```

Expected output:

```bash
rmmartins@jarvis ~ oc new-app https://github.com/ricmmartins/aro-demo-dryrun.git
--> Found image aelcee7 (5 weeks old) in image stream "openshift/php" under tag "8.0-ubi8" for "php"
Apache 2.4 with PHP 8.0
~ - - - - - - - - - - = - - - - = - = - = = =
PHP 8.0 available as container is a base platform for building and running various PHP 8.0 applications and frameworks. PHP is an HTML-embedded scripting language. PHP attempts to make it easy for developers to write dynamically generated web pages. PHP also offers built-in database integration for several commercial and non-commercia \ database management systems, so writing a database-enabled webpage with PHP is fairly simple. The most common use of PHP coding is probably as a replacement for CGI scripts.

Tags: builder, php, php80, php-80
* The source repository appears to match: php
* A source build using source code from https://github.com/ricmmartins/aro-demo-dryrun.git will be created
* The resulting image will be pushed to image stream tag "aro-demo-dryrun: latest"
* Use 'oc start-build' to trigger a new build
--> Creating resources imagestream. image. openshift.io "aro-demo-dryrun" created
buildconfig.build.openshift.io "aro-demo-dryrun" created deployment. apps "aro-demo-dryrun" created
service "aro-demo-dryrun" created
- -> Success
Build scheduled, use 'oc logs f buildconfig/aro-demo-dryrun' to track its progress.
Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
' ос expose service/aro-demo-dryrun'
Run 'ос status' to view your app.
```

### Step 6: Monitor the Deployment

To monitor the deployment process, use:

```bash
oc status
```
This command provides real-time status updates about the deployment process, helping you identify and troubleshoot any issues.

Expected output:

```bash
rmmartins@jarvis ~ oc status
Warning: apps.openshift.io/v1 DeploymentConfig is deprecated in v4.14+, unavailable in v4.10000+
In project my-php-project on server https://api.crc.testing:6443

svc/aro-demo-dryrun - 10.217.4.227 ports 8080, 8443 deployment/aro-demo-dryrun deploys istag/aro-demo-dryrun:latest <-
bc/aro-demo-dryrun source builds https://github.com/ricmmartins/aro-demo-dryrun.git on openshift/php:8.0-ubi8
deployment #2 running for 25 seconds - 1 pod
deployment #1 deployed 43 seconds ago

1 info identified, use 'oc status - suggest' to see details.
```

### Step 7: Expose Your Application

In OpenShift, a 'route' is a powerful concept that exposes a service to an external host name. Unlike a regular Kubernetes service, which typically only allows internal cluster access, a route makes your application accessible from outside the OpenShift cluster.

```bash
oc expose svc/aro-demo-dryrun
```

Expected output

```bash
rmmartins@jarvis ~ ос expose svc/aro-demo-dryrun
route. route.openshift.io/aro-demo-dryrun exposed
```

This command creates a route for your service, allowing users to access the PHP application through a publicly available URL.

### Step 8: Access the Application

Use `oc get route` to find the URL of your application:

```bash
oc get route/aro-demo-dryrun
```

Expected output

```bash
rmmartins@jarvis ~ 
NAME                    HOST /PORT                                        PATH     SERVICES          PORT      TERMINATION    WILDCARD
aro-demo-dryrun         aro-demo-dryrun-my-php-project.apps-crc.testing            aro-demo-dryrun   8080- tcp                None
```

Visit the URL in your browser to view your PHP application.

![](/assets/images/phpapp.png)

### Conclusion

Deploying an application on OpenShift Local is a beginner-friendly way to delve into the world of Kubernetes and container orchestration. This hands-on experience lays a solid foundation for more advanced OpenShift concepts and practices. As developers become more comfortable with OpenShift, they can explore its full potential in cloud environments, scaling, and managing complex, containerized applications.
