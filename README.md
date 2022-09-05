# A Version Explorer for LabVIEW

A project for exploring the challenges and possible solutions for users' pain around upgrading and downgrading projects to different LabVIEW versions.

## Background
Saving a project for a previous LabVIEW version is a pretty klunky process.  There are features (*File >> Save for Previous Version...*) that can be done for individual VIs, Libraries, and Projects.  However, this feature in LabVIEW it does not actually make the more common use cases straightforward or easy.

## Common Scenario and Recovery Workflow
Here is a description of all the steps a user goes through from the point (A) they realize their project or some of its files have been saved in a newer LabVIEW version to (B) they have fixed the problem:

1. A LabVIEW user tries to open their project using the version of LabVIEW they know to be correct -- we will call this the "pinned" LabVIEW version
2. The user discovers that some or all of the files will not open, since they are saved in a newer LabVIEW version.
3. If the user has the newer LabVIEW version (or greater) installed on that same computer then they are in luck and can continue the process. Otherwise, they will need to continue the process on a computer that hass such a newer LabVIEW version installed.
4. The user close the current (pinned) LabVIEW version.
5. The user opens a the newer version of LabVIEW (or greater version capable of downsaving to the project's pinned LabVIEW version)
6. The user opens the project files in the newer version of LabVIEW
7. The user does a Save for Previous Version operation, selecting the pinned LabVIEW version as the target version for the output files.
8. The user closes the newer LabVIEW version
9. If the user is savvy and only one or two VI files need to be down-saved, then the user may have down-saved the individual VIs "in-place" meaning they chose File >> Save for Previous Version... from the actual VIs menu and then down-saved over the accidentally up-saved file.
10. If, however, it's an lvproj, lvlib, lvclass, etc. that needs to be Saved for Previous Version... then the process cannot be done in-place and the output of Save for Previous Version... will need to be done into a new folder.  This means that the user will need to subsequently merge/copy the output files back into the project source code folder.
11. Finally, the user attempts to opens their project in the pinned (older) LabVIEW version to see if the process was successful and they are able to open all of the files. Sometimes, the user has missed a file during the "savvy" in-place process or the more detious manualy merging of files from the output folder back into the project folder.

## A Better Solution is Needed
- A much simpler solution would be some way to easily down-save project files to a desired/pinned LabVIEW version.
- Ideally, this process could be done without even having to open the newer LabVIEW version--ideally, the user could do the process from the current (correct) LabVIEW version.
- A great solution would be one that supports the command-line.  This would enable the process to be done during a build process or as part of a pre-commit hook that checks files before they are committed to the repository.

## Use Cases and Value Drivers
### Community Contributions Recieved in Newer LabVIEW Versions
- Scenario: Contributors submit code in newer LabVIEW versions than the pinned LabVIEW version.  The submitted code needs to be saved for the pinned LabVIEW vesion before it can be merged into the project project.
- Value: Makes community/team conribution to projects easier/possible for contributors who only have accesss to newer LabVIEW versions.
### Files are Accidentally Edited in Newer LabVIEW Versions
- Scenario: While working on the project, a developer accidentally opens and works on project files in a newer LabVIEW version and does not wish to throw away those changes. They wish to keep their work and simply save the changed files to the pinned LabVIEW version.
- Value: Saves developers time when they accidentally open LabVIEW files in newer LabVIEW versions.
### A Distribution Supporting Older LabVIEW Vesion is Desired
- Scenarios: The development team wishes to develop in a newer LabVIEW version (due to improved IDE usability and other features), yet they wish to build a distribution that supports earlier LabVIEW versions. As such, they wish to perform a step at build-time to save the files for a specific, earlier LabVIEW version.
-  Value: Projects can deliver more value to a wider audience since the distribution supports older LabVIEW versions.
