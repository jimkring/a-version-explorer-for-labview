# A Version Explorer for LabVIEW

This repository is an experimental project for better understanding the challenges and possible solutions for users' pain around **upgrading and downgrading projects to different LabVIEW versions** as described in the blog article [Upgrading to New LabVIEW Versions is for People Who Don’t Have Friends](https://create.vi/upgrading-to-new-labview-versions-is-for-people-who-dont-have-friends-52b85953430c).

Users want to easily work on LabVIEW projects without worrying about which version of LabVIEW the project is "supposed" to be saved in. Nor do they want to cause problem for other developers working on the project.  However, when people accidentally/inadvertently work on projects in newer LabVIEW versions it can cause problems for them and others.

## Background
Saving a project for a previous LabVIEW version is a pretty klunky process.  There is *File >> Save for Previous Version...* feature in LabVIEW that can be done for individual VIs, Libraries, Classes, and Projects.

<img width="669" alt="image" src="https://user-images.githubusercontent.com/381432/188520473-379f6d64-23c0-4005-bc33-4de091966390.png">

However, this feature does not work in a way that allows for several common and important use cases.  One key challenge with performing a "Save for Previous Version" on a project, library, or class is a result of the fact that it works differently for VIs/CTLs than it does for Projects, Libraries, and Classes; which we'll describe next.

### "Save for Previous Version" on VIs and Controls

For VIs and Controls, "Save for Previous Version" allows to specify the new, full path of the down-saved file, and even allows overwriting the existing file for an "in-place" down-save operation. That's pretty nice!

### "Save for Previous Version" on Projects, Libraries, and Classes

However for Projects, Libraries, and Classes, "Save for Previous Version" requires specifying a FOLDER where a COPY of the down-saved Project, Library, or Class (and all of its members) will be saved. This cannot be done "in place" (simply overwriting the existing files).

This makes things a bit more complicated for achieving an in-place operation.  To do this, the down-saved COPY of the files will need to be merged into the original source folder, in order to effectively down-save the files in-place. 

### User Libraries (user.lib) Comes Along for the Ride

One more problematic (for our use case) feature of "Save for Previous Version" on Projects, Libraries, and Classes is that, any dependencies located in user.lib will also be copied and down-saved into the target destination folder for the operation.

However, for our use case where the contents of user.lib are installed by OpenG and other packages, we do not want these to be copied over into the destination location.  And, if we simply delete them before merging the files back into the original location, then the down-saved files will link to the versions installed in user.lib under the MORE RECENT (original before downsaving) version of LabVIEW instead of the OLDER (after downsaving) version of LabVIEW.

There are some possibilities for how to solve this problem, but it requires jumping through some hoops and feels like a hack.

## Use Cases and Value Drivers
The following are some high-value use cases for developers.

### Community Contributions Recieved in Newer LabVIEW Versions
- Scenario: Contributors submit code in newer LabVIEW versions than the pinned LabVIEW version.  The submitted code needs to be saved for the pinned LabVIEW vesion before it can be merged into the project project.
- Value: Makes community/team conribution to projects easier/possible for contributors who only have accesss to newer LabVIEW versions.
### Files are Accidentally Edited in Newer LabVIEW Versions
- Scenario: While working on the project, a developer accidentally opens and works on project files in a newer LabVIEW version and does not wish to throw away those changes. They wish to keep their work and simply save the changed files to the pinned LabVIEW version.
- Value: Saves developers time when they accidentally open LabVIEW files in newer LabVIEW versions.
### A Distribution Supporting Older LabVIEW Vesion is Desired
- Scenarios: The development team wishes to develop in a newer LabVIEW version (due to improved IDE usability and other features), yet they wish to build a distribution that supports earlier LabVIEW versions. As such, they wish to perform a step at build-time to save the files for a specific, earlier LabVIEW version.
-  Value: Projects can deliver more value to a wider audience since the distribution supports older LabVIEW versions.


## Workflow for Recovering from Unwanted Source Code Upgrade
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

A good possible solution to this would be to be able to "pin" a project to a specific LabVIEW version [as desribed on Jim Kring's blog](https://create.vi/upgrading-to-new-labview-versions-is-for-people-who-dont-have-friends-52b85953430c#4891) and shown in the screenshot below:

![image](https://user-images.githubusercontent.com/381432/188509924-f87d937b-bd16-4f39-9b3e-818b7d81b28c.png)

## Notes on Possible Solutions

### A Version Explorer UI

A working protype is shown below.

<img width="672" alt="image" src="https://user-images.githubusercontent.com/381432/188572165-66488776-cdf5-44fa-9ebc-2e268600c6e3.png">

The source for this VI is here: `.\experiments\save-for-previous-version\source\A Version Explorer - UI.vi`

This prototype works as follows:

- Saves VIs/CTLs in place using the `VI.Save for Previous` method
- Saves LVLIBs and LVCLASSes to a temp directory (using the `Save for Previous` methods of those project item types) and then copies the output .lvlib or .lvclass file, replacing the original file.
- Directly modifies the .lvproj file XML data, setting the version information.

This seems to work pretty well.

### Solving the Copy of user.lib Problem
In thinking about a solution to the user.lib problem, basically what's needed is to have the final output (the down-saved VIs, in place) link to the installed user.lib VIs instead of the ones from the newer LabVIEW version.

Considerations and Ideas:
- If the older/target LabVIEW version is installed and also has ALL OF THE REQUIRED USER.LIB VIs installed, then would be possible to re-write/fix the linker info of the exported VIs in the older version of LabVIEW.
    - Is it reasonable to assume that the user has the older LabVIEW version on the same machine as the newer LabVIEW version?
- We cannot re-write the linker info in the newer version of LabVIEW (which we know has those required VIs installed) because "Write Linker Info" cannot operate on VIs from older LabVIEW Versions (it will raise an error message about the VIs being too old to upgrade).

