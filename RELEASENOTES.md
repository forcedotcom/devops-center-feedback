# DevOps Center Patch Release Notes

Two, four, six, eight, who do we appreciate? You! For finding these bugs. Here's a list of bugs that you reported and in which patch they were fixed.

## v 4.x.x (July 8, 2022)

* FIXED: Error: `Sorry to interrupt: An internal server error has occurred` Can't open the DevOps Center app if Lightning Web Security is enabled in the org. [#164](https://github.com/forcedotcom/devops-center-feedback/issues/164)
* FIXED: Post-install script fails to run when package installed to an org with ETM2 (Territory Management) enabled. [#173](https://github.com/forcedotcom/devops-center-feedback/issues/173)
* FIXED: Can't commit a layout with a special character in its name. [#187](https://github.com/forcedotcom/devops-center-feedback/issues/187)
* FIXED: Can't install DevOps Center package in a Professional Edition org. **Note:** Professional Edition orgs don't have API access by default. To use DevOps Center, users must have the API Enabled system permission assigned via a permission set or profile.
