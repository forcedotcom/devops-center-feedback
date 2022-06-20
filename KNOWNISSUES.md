# Known Issues

## Project List View Loads Slowly

### Issue
Sometimes the Projects page can take several minutes to load, and it appears that nothing is happening. 

### Workaround
None. We're working on addressing this issue.

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

