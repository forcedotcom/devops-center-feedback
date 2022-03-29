# Known Issues

## Source Tracking Lost After a Connected Sandbox Is Refreshed

### Issue
If you refresh a sandbox used in an active DevOps Center project, source tracking is lost and DevOps Center can't identify changes files. 

### Workaround
If you don't have access to the Setup menu, ask your Salesforce admin for help.

1. From Setup, enter `Named Credentials` in the Quick Find box, then select **Named Credentials**.
2. Click **Edit** next to the refreshed sandbox (development environment).
3. Change the URL by appending a couple random characters to the end of it, then click **Save**.
4. Back in DevOps Center, add the sandbox as if it were a new development environment.
5. Log in and authorize the sandbox.
6. (Optional) Rename the old sandbox to distinguish it as "deprecated" ???

## Can’t Move Promoted Changes Back to a Previous Pipeline Stage

### Issue
You’ve promoted changes to your QA or staging environment but you realize that you want or need to make additional changes before you can release these changes to your customers. For example, you need to add more tests or you missed an important requirement.

**Note:** The workaround assumes you haven't made any changes directly in a downstream pipeline stage branch or pipeline environment. If you have, you'll need to manually re-create those changes in a development environment.

### Workaround
1. First, how many developers (and development environments) are involved?

- If there’s only one developer (and development environment) involved in these changes, and all the files and metadata exist in that development environment, skip to the next step. 
- If multiple developers (and development environments) are involved, all the changes can be found in the first pipeline stage’s branch, such as Integration. Synchronize your development environments so that they have all the changes in the first stage’s branch. 

2. Open one or more new work items.
2. Make the required changes.
2. Promote those changes through the pipeline.

## Managed Package Installation Failure - The post install script failed

### Issue
The installation fails and you get an email that looks something like this:

```
Subject: Sandbox: Package DevOps Center - Beta Install Failed
Importance: High

Your request to install package "DevOps Center - Beta 3.3.0" was unsuccessful. None of the data or setup information in your salesforce.com organization was affected.

If your install continues to fail, contact Salesforce CRM Support through your normal channels and provide the following information.

Organization: My Company (org-id)
User: Jane Doe (user-id)
Package: DevOps Center - Beta (04t6g000008ffbt)

Problem:

1.  Unexpected Error
The package installation failed. Please provide the following information to the publisher:

Organization Name: Acme Corporation ID: org-id
Package: DevOps Center - Beta
Version: 3.3
Error Message: The post install script failed.
```

### What Happened
During package installation, our post-installation Apex script is attempting to create a new permission set for our users who can manage environments. This permission set is named `sf_devops_InitializeEnvironments` and is setting the following permissions:

    Modify Metadata Through Metadata API Functions (required for our use cases)
    View Setup and Configuration (required by Modify Metadata Through Metadata API Functions)
    View Roles and Role Hierarchy (required by Modify Metadata Through Metadata API Functions) 
    Customize Application (required for our use cases)
    Manage Custom Permissions (required by Customize Application)

If your org has Translation Workbench enabled, there’s another dependency for Customize Application:

    Modify Metadata Through Metadata API Functions (required for our use cases)
    View Setup and Configuration (required by Modify Metadata Through Metadata API Functions)
    View Roles and Role Hierarchy (required by Modify Metadata Through Metadata API Functions)
    Customize Application (required for our use cases)
    Manage Custom Permissions (required by Customize Application)
    Manage Translation (required by Customize Applications)

In this case, our package installation post-install script fails, because our script doesn't include the Manage Translation permission.

### Workaround
[Create the permission set manually via the Setup UI](https://help.salesforce.com/s/articleView?id=sf.perm_sets_create.htm&type=5), and then install the DevOps Center package again. This fix should resolve the issue, because our post-install script first checks for the presence of the permission set (so that we don’t fail during re-installation scenarios), and doesn’t attempt to create the permission set if it already exists in the org. The Permission Set details are:

    API Name: sf_devops_InitializeEnvironments
    Label: sf_devops_InitializeEnvironments
    Description: Allows managers of the DevOps Center to manage connections to development environments. This requires the Modify Metadata Through Metadata API Functions and Customize Application permissions.
    Permissions:
        Modify Metadata Through Metadata API Functions (and its dependent permissions)
        Customize Application (and its dependent permissions)

### Follow-up
The DevOps Center development team has logged work item W-10705695 for this issue, and will triage and prioritize a fix for future customers. Please reference W-10705695 when sending details of this issue to the DevOps Center team.
