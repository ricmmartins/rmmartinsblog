---
date: '2025-01-22'
title: What to consider when using Azure AD as IDP?
tags: ["Azure", "IDP", "ARO"]
---

<div class="notice--info">
This article was originally published on September 21, 2023, at <a href="https://cloud.redhat.com/experts/idp/considerations-aad-ipd/" target="_blank">https://cloud.redhat.com/experts/idp/considerations-aad-ipd/</a>
</div>

In this guide, we will discuss key considerations when using Azure Active Directory (AAD) as the Identity Provider (IDP) for your ARO or ROSA cluster. Below are some helpful references:

* [Configure ARO to Use Azure AD](https://cloud.redhat.com/experts/idp/azuread-aro/)
* [Configuring IDP for ROSA, OSD, and ARO](https://docs.openshift.com/rosa/cloud_experts_tutorials/cloud-experts-entra-id-idp.html)

## Default Access for All Users in Azure Active Directory

Once you set up AAD as the IDP for your cluster, it's important to note that by default, all users in your Azure Active Directory instance will have access to the cluster. They can log in using their AAD credentials through the OpenShift Web Console endpoint:

![OpenShift Web Console Login](../assets/images/aro-login.png)

However, for security purposes, it's recommended to restrict access and only allow specific users who are assigned to access the cluster.

## Restricting Access

To implement access restrictions, follow these steps:

1. Log in to the Azure Portal and navigate to your AAD instance.

2. Under Enterprise applications, select the application created for the ARO IDP configuration.

![Select Application](../assets/images/pick-application.png)

3. In the selected Enterprise application, go to Properties and switch the **"Assignment required?"** option to **YES**.

![Assignment Required](../assets/images/assignment-required.png)

4. If you attempt to log in at this point, you will receive a denial error:

Enter your username:

![Login Attempt](../assets/images/login-attempt.png)

Enter your password:

![Login Attempt 2](../assets/images/login-attempt-2.png)

The error message indicates that only users specifically granted access to the application are allowed:

![Access Denied](../assets/images/access-denied.png)

5. To allow access, go to Users and groups in the main blade, click **+ Add user/group**, and add the desired users/groups who should have access to the ARO cluster.

![Add User](../assets/images/add-user.png)

Search for the desired user/group and click **Select**.

![Assign User](../assets/images/assign-user.png)

Verify that the user has been assigned:

![User Assigned](../assets/images/user-assigned.png)

6. You should now be able to log in with the specified user/group to your cluster:

Enter your username:

![Login Attempt](../assets/images/login-attempt.png)

Enter your password:

![Login Attempt 2](../assets/images/login-attempt-2.png)

You will then be logged in:

![Logged In](../assets/images/logged-in.png)

## Approval Workflow

If you receive a message like the one below, it means that your AAD has the [admin consent workflow](https://learn.microsoft.com/en-us/azure/active-directory/manage-apps/configure-admin-consent-workflow) enabled:

![Approval Required](../assets/images/approval-required.png)

In this case, you will need to request and wait for approval from your AAD domain admin. 
To request access, fill out the request form:

![Approval Request](../assets/images/approval-request.png)

And wait for approval:

![Request Sent](../assets/images/request-sent.png)

## Self-Approval Process

If you have administrative privileges, you can self-approve the request by following these steps:

> Please note that these steps are based on the official guidance from Microsoft, which is [available here.](https://learn.microsoft.com/en-us/azure/active-directory/manage-apps/review-admin-consent-requests)


1. Go to your Azure Active Directory Tenant > Enterprise Applications > Admin Consent Requests > All (Preview):

![Admin Consent Request](../assets/images/admin-consent-requests.png)

2. Select the application (openshift, in this case) and click **Review permissions and consent**:

![Details Admin Consent Request](../assets/images/details-admin-consent-requests.png)

3. A new window will open, prompting you to log in with credentials of an admin with permissions:

![Admin Login](../assets/images/admin-login.png)

4. Click **Accept** to consent to the permission:

![Permissions Requested](../assets/images/permissions-requested.png)

You will then see that the request was approved:

![Request Approved](../assets/images/request-approved.png)

Now you will be able to log in through the AAD option:

![OpenShift Web Console Login](../assets/images/aro-login.png)

Enter your username:

![Login Attempt](../assets/images/login-attempt.png)

Enter your password:

![Login Attempt 2](../assets/images/login-attempt-2.png)

It worked!

![Logged In](../assets/images/logged-in.png)

As a best practice, we recommend removing the kubeadmin user after setting up an identity provider. You can find instructions on how to do this [here](https://docs.openshift.com/container-platform/4.13/authentication/remove-kubeadmin.html).

## Using the Group Sync Operator

Integrating groups from external identity providers with OpenShift, such as synchronizing groups from AAD, can be a valuable feature to enhance your system's functionality. To accomplish this, you can leverage the usage of the [Group Sync Operator](https://github.com/redhat-cop/group-sync-operator). 

We have published a comprehensive how-to guide that walks you through the process, [accessible here](https://cloud.redhat.com/experts/idp/az-ad-grp-sync/). By following these instructions, you'll be able to seamlessly synchronize AAD groups into your OpenShift environment, optimizing your workflow and streamlining access management.
