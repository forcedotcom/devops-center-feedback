# Managed Package Installation Failure - The post install script failed

### Issue
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
