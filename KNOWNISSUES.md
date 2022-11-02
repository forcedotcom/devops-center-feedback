# Known Issues

Here the workarounds for these known issues (bugs), which we plan to address or fix in a future release. Also see the Troubleshooting topics in Salesforce help for other errors and workarounds.

[Troubleshoot DevOps Center Configuration](https://help.salesforce.com/s/articleView?id=sf.devops_center_troubleshooting_setup.htm)

[Troubleshoot DevOps Center Errors](https://help.salesforce.com/s/articleView?id=sf.devops_center_troubleshooting.htm)

## DevOps Center Isn't Deploying Translation Consistently

### Issue
DevOps Center deploys only the first translation `-meta.xml` file instead of the `objectTranslation-meta.xml file`.

### Workaround
None. We're working on addressing this issue.

## Can't See All Repos Branches When Setting Up Pipeline

### Issue
When you're building your pipeline and want to select a branch from the drop-down, you can't see all the branches available in the repository. Consequently, you can't select the branch you want to select.

### Workaround
1. On the Pipeline Environments, click **Edit Branch**.
2. From the Use an Existing Branch drop-down, select any branch, then click **Save**.
3. Click **Edit Branch** again.
4. Select **Create a branch for me from this stage**.
5. For the Branch Name, enter the existing branch's name, then click **Save**. Because DevOps Center doesn't allow duplicate branch names, it finds the existing branch and uses it. 
6. Activate the pipeline.

If you get a validation error when activating the pipeline, open a [GitHub issue](https://github.com/forcedotcom/devops-center-feedback/issues).

## DevOps Center Records Count Against Org Storage Limits

### Issue
We've previously stated that DevOps Center doesn't count against org storage limits (for example, object records), but it currently does. If you installed DevOps Center in a Developer Edition org, you're more likely to run into this issue because storage limits are lower.

See [Data and File Storage Allocations](https://help.salesforce.com/s/articleView?id=sf.overview_storage.htm&type=5) in Salesforce Help to view storage limits for your Salesforce edition.

## Can't Install DevOps Center in a Professional Edition Org

**Note:** Fixed in patch 4.3.0

### Issue
An unforseen issue occurs when the post installation script runs. 

### Workaround
None. However, you can install DevOps Center in another supported org edition, such as Developer Edition. We plan to fix this issue in a near-future release.

## "Sorry to Interrupt: An internal server error has occurred" when opening DevOps Center

**Note:** Fixed in patch 4.3.0

### Issue
Right now, DevOps Center isn't compatible with Lightning Web Security. We plan to fix this issue in a near-future release.

### Workaround
Disable Lightning Web Security.

1. From Setup, in the Quick Find box, enter `Session Settings`, and then select **Session Settings**.
2. On the Session Settings page, deselect **Use Lightning Web Security for Lightning web components** and save.

## Package Installation Failed - The post install script failed

### Issue
During package installation, our post-installation Apex script is attempting to create a new permission set for our users who can manage environments. This permission set is named `sf_devops_InitializeEnvironments` and is setting the following permissions:

    * Modify Metadata Through Metadata API Functions (required for our use cases)
    * View Setup and Configuration (required by Modify Metadata Through Metadata API Functions)
    * View Roles and Role Hierarchy (required by Modify Metadata Through Metadata API Functions) 
    * Customize Application (required for our use cases)
    * Manage Custom Permissions (required by Customize Application)

**Note:** Enterprise Territory Management issue fixed in patch 4.3.0

If your org has [Enterprise Territory Management](https://help.salesforce.com/s/articleView?id=sf.tm2_intro.htm&type=5) enabled, there's another dependency for Customize Application:

    * Modify Metadata Through Metadata API Functions (required for our use cases)
    * View Setup and Configuration (required by Modify Metadata Through Metadata API Functions)
    * View Roles and Role Hierarchy (required by Modify Metadata Through Metadata API Functions)
    * Customize Application (required for our use cases)
    * Manage Custom Permissions (required by Customize Application)
    * Manage Territories (required by Customize Application)

In this case, our package installation post-install script fails, because our script doesn't include the Manage Territories permission.

### Workaround
[Create the permission set manually via the Setup UI](https://help.salesforce.com/s/articleView?id=sf.perm_sets_create.htm&type=5), and then install the DevOps Center package again. This fix should resolve the issue, because our post-install script first checks for the presence of the permission set (so that we don't fail during re-installation scenarios), and doesn't attempt to create the permission set if it already exists in the org. The Permission Set details are:

    * API Name: sf_devops_InitializeEnvironments
    * Label: sf_devops_InitializeEnvironments
    * Description: Allows managers of the DevOps Center to manage connections to development environments. This requires the Modify Metadata Through   Metadata API Functions and Customize Application permissions.
    * Permissions:
        * Modify Metadata Through Metadata API Functions (and its dependent permissions)
        * Customize Application (and its dependent permissions)

### Follow-up
The DevOps Center development team has logged work item W-11336266 for this issue, and will triage and prioritize a fix for future customers. Please reference W-11336266 when sending details of this issue to the DevOps Center team.

## Project List View Loads Slowly

### Issue
Sometimes the Projects page can take several minutes to load, and it appears that nothing is happening. 

### Workaround
Wait until the Projects list view loads. We're working on addressing this issue.

## Can't Promote Changes to Production If Default Apex Test Option Is Selected

### Issue
When promoting changes to production, you select Default (no tests are run) as the Apex test option. You see this error:

```
INVALID_OPERAITON: testLevel of NoTestRun cannot be used in production organizations
```

### Workaround
Select `Run Local Tests` instead.

## Source Tracking Lost After a Connected Sandbox Is Refreshed

### Issue
If you refresh a sandbox used in an active DevOps Center project pipeline, source tracking is lost and DevOps Center can't identify changed files. 

### Workaround
If you don't have access to the Setup menu, ask your Salesforce admin for help.

1. From Setup, enter `Named Credentials` in the Quick Find box, then select **Named Credentials**.
2. Click **Edit** next to the Named Credential record corresponding to the refreshed sandbox (development environment).
3. Change the URL by appending a couple random characters to the end of it, then click **Save**.
4. Back in DevOps Center, add the sandbox as if it were a new development environment.
5. Log in and authorize the sandbox.
6. (Optional) Rename the "old" sandbox instance's Environment record to distinguish it as "deprecated".
    1. Go to `https://<domain>.lightning.force.com/lightning/o/sf_devops__Environment__c/list?filterName=Recent`
    2. Create a new list view.
    3. From the dropdown menu next to the "old" sandbox's Environment record, select **Edit**.
    4. Change the name to something like: `Dev Sandbox deprecated`, then click **Save**.

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

