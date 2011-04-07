# Submitting Patches #

This page describes the full process of submitting a patch to the AOSP, including reviewing and tracking changes with Gerrit.

## Prerequisites ##

- Before you follow the instructions on this page, you will need to set up your
local working environment and get the Android source files. For instructions,
follow the "Getting Started" section [here](downloading.html).

- For details about Repo and Git, see [Version Control](version-control.html).

- For information about the different roles you can play within the Android
Open Source community, see [Project roles](/source/roles.html).

- If you plan to contribute code to the Android platform, be sure to read
the [AOSP's licensing information](/source/licenses.html).

- Note that changes to some of the upstream projects used by Android should be
made directly to that project, as described in [Upstream Projects](#upstream-projects).

## Patch Etiquette ##

### Writing patch descriptions ###

Each change list description will be pushed to the public AOSP repository. Please follow our guidelines for writing change descriptions in order to ensure your change is reviewed and approved.

- Start with a one-line summary, 50-60 characters max, followed by a blank line. This format is used by git and gerrit for various displays.

- The description should focus on what issue it solves, and how it solves it. The second part is somewhat optional when implementing new features, though desirable.

- The description should be self-contained. It should not rely on people having access to any other information.

- The description should be reasonably descriptive. You've spent a few hours or a few days working on your change so you can spend 5 minutes writing a proper description. Maybe write it while you do a final compile before submitting.

## Using the Gerrit code-review tool ##

You can open Gerrit by visiting whatever URL is returned to you from the repo upload command, 
or by visiting [https://review.android.com](https://review.source.android.com)

### Viewing the status of uploaded changes ###

To check the status of a change that you uploaded, open [Gerrit](https://review.source.android.com/mine), 
sign in, and click MyChanges.

### Reviewing a change ###

If you are assigned to be the Approver for a change, you need to determine the following:

- Does this change fit within this project's stated purpose?

- Is this change valid within the project's existing architecture?

- Does this change introduce design flaws that will cause problems in the future?

- Does this change follow the best practices that have been established for this project?

- Is this change a good way to perform the described function?

- Does this change introduce any security or instability risks?

If you approve of the change, mark it with LGTM ("Looks Good to Me") within Gerrit.

### Verifying a change ###

If you are assigned to be the Verifier for a change, you need to do the following:

- Patch the change into your local client using one of the Download commands.

- Build and test the change.

- Within Gerrit use Publish Comments to mark the commit as "Verified" or "Fails," and add a message explaining what problems were identified.

### Viewing diffs and comments ###

To open the details of the change within Gerrit, click on the "Id number" or "Subject" of a change. To compare the established code with the updated code, click the file name under "Side-by-side diffs."

### Adding comments ###

Anyone in the community can use Gerrit to add inline comments to code submissions. A good comment will be relevant to the line or section of code to which it is attached in Gerrit. It might be a short and constructive suggestion about how a line of code could be improved, or it might be an explanation from the author about why the code makes sense the way it is.

To add an inline comment, double-click the relevant line of the code and write your comment in the text box that opens. When you click Save, only you can see your comment.

To publish your comments so that others using Gerrit will be able to see them, click the Publish Comments button. Your comments will be emailed to all relevant parties for this change, including the change owner, the patch set uploader (if different from the owner), and all current reviewers.

### After a submission is approved ###

After a submission makes it through the review and verification process, Gerrit automatically merges the change into the public repository. The change will now be visible in gitweb, and others users will be able to run `repo sync` to pull the update into their local client.

### Downloading changes from Gerrit ###

A submission that has been verified and merged will be downloaded with the next `repo sync`. If you wish to download a specific change that has not yet been approved, run

    $ repo download TARGET CHANGE

where TARGET is the local directory into which the change should be downloaded and CHANGE is the 
change number as listed in [Gerrit](https://review.source.android.com/). For more information, 
see the [Repo reference](/source/using-repo.html).

### How do I become a Verifier or Approver? ###

In short, contribute high-quality code to one or more of the Android projects.
For details about the different roles in the Android Open Source community and
who plays them, see [Project Roles](/source/roles.html).

## Using GitWeb to track patch histories ##

To view snapshots of the files that are in the public Android repositories and view file histories, use the [Android instance of GitWeb](http://android.git.kernel.org/).

<a name="upstream-projects"></a>

## Upstream Projects ##

Android makes use of a number of other open-source projects, such as the Linux kernel and WebKit, as described in
[Branches and Releases](/source/code-lines.html). For the upstream projects detailed below, changes should be made directly upstream. Such changes will be incorporated into the Android tree as part of the usual process of pulling these projects.

### WebKit ###

All changes to the WebKit project at `external/webkit` should be made
upstream at [webkit.org](http://www.webkit.org). The process begins by filing a WebKit bug. 
This bug should use `Android` for the `Platform` and `OS` 
fields only if the bug is specific to Android. Bugs are far more likely to receive the reviewers'
attention once a proposed fix is added and tests are included. See
[Contributing Code to WebKit](http://webkit.org/coding/contributing.html) for details.

### V8 ###

All changes to the V8 project at `external/v8` should be submitted upstream at
[code.google.com/p/v8](http://code.google.com/p/v8). See [Contributing to V8](http://code.google.com/p/v8/wiki/Contributing)
for details.

