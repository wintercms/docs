# Maintainers Guide to the Galaxy

- [Preface](#preface)
- [Roles](#roles)
- [Main Philosophy](#main-philosophy)
- [Projects](#projects)
    - [Winter CMS](#winter-cms)
        - [Main Branches](#winter-cms-main-branches)
        - [Milestones](#winter-cms-milestones)
        - [Reviewing Issues and Pull Requests](#winter-cms-reviewing-issues-and-prs)
        - [Merging Pull Requests](#winter-cms-merging-pull-requests)
        - [Standard Workflow for Maintainers](#winter-cms-standard-workflow)
        - [Release Process](#winter-cms-release-process)
    - [First party plugins](#plugins)
        - [Main Branches](#plugins-main-branches)
        - [Milestones](#plugins-milestones)
        - [Reviewing Issues and Pull Requests](#plugins-reviewing-issues-and-prs)
        - [Merging Pull Requests](#plugins-merging-pull-requests)
        - [Standard Workflow for Maintainers](#plugins-standard-workflow)
        - [Release Process](#plugins-release-process)

<a name="preface"></a>
## Preface

This document immortalises the processes involved with maintaining the Winter CMS repositories. This is intended to give the maintainers of the project a clear idea as to how to review and manage the inflow of code and ensure the qualities and philosophies of Winter CMS and its first-party plugins are maintained.

The Winter CMS project is the result of a fork that occurred on March 5th, 2021 after it became apparent to the maintainer group of October CMS that the original founders of October CMS had no intention of working collaboratively with the maintainer group. [The announcment of the fork](https://github.com/wintercms/winter/issues/5) goes into more details on what occurred as well as the reasoning for the fork.

This document was originally written for October CMS, but has been repurposed for Winter CMS.

<a name="roles"></a>
## Roles

The project is considered a flat-level of management with equal responsibility of the project between all the maintainers. The current maintainers (as of October 11th, 2021) are as follows:

- [@LukeTowers](https://github.com/LukeTowers) - Luke Towers
- [@bennothommo](https://github.com/bennothommo) - Ben Thomson
- [@mjauvin](https://github.com/mjauvin) - Marc Jauvin
- [@jaxwilko](https://github.com/jaxwilko) - Jack Wilkinson

Luke Towers has been designated the Lead Maintainer by the maintainer group, as the longest-serving maintainer of October CMS.

<a name="main-philosophy"></a>
## Main Philosophy

Winter CMS operates under the philosophy that "Everything should be made as simple as possible, but not simpler", as it was for October CMS before it. This phrase has been attributed to Albert Einstein ([although this has been disputed](https://quoteinvestigator.com/2011/05/13/einstein-simple/)), and succinctly describes the ethos of Winter CMS.

As arbiters of the codebase, maintainers should strive to ensure that as the functionality and potential of the system increases, careful thought is still given to ensuring that proposed code is simple to develop for the developer and easy to use for the user.

As Winter CMS aims to be an "evergreen" product with rolling updates, special care must be taken to maintain backwards compatibility and allow all users of Winter CMS and the first-party plugins to continue to use the products through updates without issue.

At the same time, Winter CMS has a responsibility to its users to ensure that best practices in security and code are followed.

Where possible, Winter CMS follows the LTS (long-term support) versions of the Laravel framework as the foundation framework, which does include the deprecation of unsupported PHP versions as required.

<a name="projects"></a>
## Projects

<a name="winter-cms"></a>
### Winter CMS

The following protocols are in place for all current and future repositories underneath the [@wintercms](https://github.com/wintercms) organisation. This includes the [Winter CMS](https://github.com/wintercms/winter) and [Storm library](https://github.com/wintercms/storm) repositories, as well as its [installer](https://github.com/wintercms/web-installer) and [documentation](https://github.com/wintercms/docs).

<a name="winter-cms-main-branches"></a>
#### Main Branches

Winter CMS and the Storm library both have one main branch for their repositories - `develop`, where all ongoing development for the next release on the current branch is stored. Both repositories also contain a branch for each of the main release branches (including older,
end-of-life branches that may still receive small updates and security fixes from time to time).

For the installer and documentation repositories, the `main` branch represents the latest development snapshot. Commits to the `main` branch can be done on an as-needed basis, as long as commits are small and are clearly explained and justified in the commit message. It is still preferred that pull requests are used for larger commits. There is no `develop` branch for these repositories.

<a name="winter-cms-milestones"></a>
#### Milestones

The Winter CMS repositories use milestones to track which changes are to be released with each build.

- `v1.0.459`
- `v1.0.460`
- `v1.0.461`
- `v1.1.1`

In these examples `v1` reflects the fact that Winter strives to never introduce massive breaking changes, the second number is associated with the LTS version of Laravel supported by that branch, and the final number represents the release number for that branch. The current Winter CMS branches and their associated LTS release versions are as follows:

- `v1.2` - Preparation for Laravel 9.x LTS
- `v1.1` - Laravel 6.x LTS (Currently active branch)
- `v1.0` - Laravel 5.5 LTS (Only receives security updates from Winter, not recommend for active use)

One milestone representing the *upcoming* build to be released will be present for all incoming code changes, as well as a milestone representing the *next* build to be released for issues / PRs that are slightly higher risk and are being delayed until the next release to avoid blocking the current one. Additionally, a build milestone for the first version of the next branch will be present to indicate that the issue / PR has potentially breaking changes that will be delayed until the next major branch introducing the next Laravel LTS release. For example, if the last build released was `v1.1.7`, there will be a milestone for `v1.1.8` and `v1.2.0`.

The maintainers will, when appropriate, deem a milestone to be locked from further changes except for bug fixes or maintenance in order to proceed with releasing the build. In these cases, no new issues or PRs should be assigned to the milestone unless they are a bug fix, or are some form of maintenance. No new features should be assigned to these milestones.

Once the build is released by the maintainers, the Milestone should be locked completely and closed.

<a name="winter-cms-reviewing-issues-and-prs"></a>
#### Reviewing Issues and Pull Requests

Maintainers must review all issues and pull requests that are made to the Winter CMS repositories to ensure that they meet the standards of code quality and adherence to the principles of Winter CMS.

Things that maintainers should look at with issues should include:

- If it is a reported bug, does the bug exist? Sometimes bugs are due to configuration issues or unique environments.
- If it is a suggested enhancement or change, does it solve a problem that Winter does not yet solve?
- If it is a discussion, it should be moved to the Discussions section.

When reviewing pull requests, maintainers should strive to look at the following:

- Does the pull request actually solve the issue it intends to resolve?
- Does the pull request follow the [PSR-2 guidelines](https://www.php-fig.org/psr/psr-2/) and our own [Developer Guidelines](../help/developer/guide)?
- Does the pull request allow users of Winter CMS to continue to use the system as they have up until now, or provide adequate justification for the change in behaviour?

Pull requests submitted by contributors must be made to the `develop` branch or to a feature or fix branch.

Pull requests should also indicate any issues that they resolve - this should be stipulated as "Fixes #..." followed by the issue number in the initial (opening) comment of a pull request thread.

Maintainers have free reign to ask for clarification on issues and PRs and seek further information from the submitter. If more details are not forthcoming, maintainers may close the issue or PR. In addition, issues and PRs are automatically closed after 60 days of inactivity (this is simply to keep the focus on what matters to users, auto-closed issues are happily revisted when requested).

To keep track of the progress of issues and pull requests, we use the labelling and milestone systems of GitHub to record the current status. Our [standard labels](https://github.com/wintercms/meta/blob/master/github/labelling.md) dictate a type of pull request or issue (ie. an enhancement, maintenance, discussions or bug report), a status (ie. in progress, completed, or a response or revision is needed) and sometimes a priority (ie. critical, high or low). In general, an issue should have one type label and one status label.

Once a build has been decided, the milestone should be set to the milestone of the build it will be released in.

<a name="winter-cms-merging-pull-requests"></a>
#### Merging Pull Requests

When a Pull Request is assigned to the *current* build milestone, it can be merged into the `develop` branch. Maintainers should use the **Squash and merge** option in GitHub to squash PRs into one commit that is then merged into the `develop` branch. This function creates a commit with the name of the PR followed by the PR number in parenthesis to give a quick and useful link back to the merged PR. Note that if the commit history of the PR provides meaningful value to the project without cluttering it with unnecessary commits a Merge Commit may be used to include the history of the PR into the project as well instead of squashing it down to one commit.

The commit message should contain extra information about the merged PR if it is applicable - this should include any justifications for the change and important info that might be required for future reference.

Users who have provided fixes or assistance in the form of PRs and commits are properly attributed in Git, however, if someone provides assistance outside of Git (ie. through Discord or elsewhere in the community), it is generally polite to credit them in the commit.

Credit should be provided in the form of `Credit to @username` in the commit description.

In addition, any fixed or referenced issues or PRs should be linked as well, tagged as either `Fixed:` or `Refs:` respectively. GitHub will automatically link any issue numbers added to the same repository, but external repositories should be linked as a URL.

An example commit message is below:

```
Fix some issues in the code (#1234)

This fixes a couple of issues spotted in the Backend. The changes are minor and should not affect backwards compatibility.

Credit to @bennothommo. Fixes #1212 and https://github.com/wintercms/wn-blog-plugin#432
```

A merged pull request - and any linked issues that the pull request resolves - should have the `Status: Completed` label assigned to them and all other Status labels should be unassigned. If the pull request does not have a build milestone assigned to it yet, this should also be changed to the current build milestone.

<a name="winter-cms-standard-workflow"></a>
#### Standard Workflow for Maintainers

Where possible, all work done on Winter CMS should be done through pull requests. Pull requests allow the opportunity for all maintainers to be able to review the proposed changes and provide feedback or determine any perceived issues, and in addition allow the automated tests to pick up any issues with code quality or our test scenarios before it reaches the main branches.

Maintainers should create a branch for their incoming changes - using the following format for the branch name:

- `wip/` followed by the feature name in [kebab-case](https://en.wiktionary.org/wiki/kebab_case) for a new feature.
- `fix/` followed by either a fix name in [kebab-case](https://en.wiktionary.org/wiki/kebab_case), or alternatively, an issue number if the fix is to resolve a reported issue on GitHub.

Pull requests must be made to the `develop` branch and provide a succint description as the title of the pull request, with more information as required for other maintainers to understand the changes implemented.

Direct commits to the `develop` branch should be avoided where possible in favour of pull requests. A few circumstances exist where a commit straight to `develop` may be necessary:

- Fixing parse errors or exceptions that may have been missed during code review of a PR.
- Documentation fixes.
- Fixing disclosed security vulnerabilities.
- Minor tweaks / bug fixes that do not change any current behaviours of Winter CMS.

<a name="winter-cms-release-process"></a>
#### Release process

The release process is one undertaken by the maintainers to release a new build of Winter CMS.

The following steps are undertaken by the maintainers for release:

- The release notes are finalized in the `winter/meta` repository.
- The `develop` branch is merged into the current active mainline version branch (i.e. `v1.1`) tagged with the *current* milestone's version, ie. `v1.1.7`.
- A release is published for the tag in GitHub using the finalised release notes from the meta repository.
- Once tagged, a subsplit of the repository is actioned which separates the modules of Winter CMS from the main repository to make them available to be pulled in separately via Composer.
- The [releases page on the Winter CMS website](https://wintercms.com/releases) is automatically updated to include the newest release and its details.

<a name="plugins"></a>
### First-party plugins

The following protocols are in place for all first-party plugins published in the Winter CMS organisation. Note that a lot of the processes are the same as for Winter CMS, so this section defines any *differences* in the processes to be used.

<a name="plugins-main-branches"></a>
#### Main Branches

All first-party plugin repositories contain a `main` branch which is the combined efforts of all contributions to the plugin. Maintainers may commit changes to the `main` branch on an as-needed basis, but as with the Winter CMS repositories, it is still preferred that substantial changes or new or changed features be done in pull requests.

<a name="plugins-milestones"></a>
#### Milestones

First-party plugins, like the Winter CMS repositories, use milestones to track which changes are implemented with which version of the plugin. Milestones in the first-party plugins use a more "semantic" versioning and so have major, minor and point releases in the format of `major.minor.point`. For example:

- `v1.0.1`
- `v1.1.0`
- `v1.1.1`

The following scenarios would determine which version number needs to change:

- `major` should be increased for substantial changes made to the plugin, such as complete rewrites or pivoting of the purpose of the plugin. These changes are assumed to be backwards-incompatible and will require manual intervention by the users of the plugin.
- `minor` should be increased for smaller changes or new features that may still be backwards-incompatible with adequate justification. This can include changes to the database schema or changes to component settings.
- `point` should be increased for minor fixes, translation updates or very minor new features that maintain backwards compatibility.

Maintainers should make sure that a `minor` and a `point` milestone is available for first-party plugins at all times, and use their best determination of which is the appropriate milestone for all incoming PRs and issues.

<a name="plugins-reviewing-issues-and-prs"></a>
#### Reviewing Issues and Pull Requests

Maintainers should use the [same processes for Winter CMS](#winter-cms-reviewing-issues-and-prs) when reviewing issues and PRs for first-party plugins.

Pull requests should be made to the `main` branch at all times, unless it is a child feature branch that is to be merged into a parent feature branch.

For pull requests that contain changes to the `updates/version.yaml` file, maintainers should request that contributors do not include these changes in their pull request. This file should only be modified as part of the [Release Process](#plugins-release-process).

<a name="plugins-merging-pull-requests"></a>
#### Merging Pull Requests

As with reviewing issues and PRs, first-party plugins use the [same processes as Winter CMS](#winter-cms-merging-pull-requests) when merging pull requests.

<a name="plugins-standard-workflow"></a>
#### Standard Workflow for Maintainers

The workflow for first-party plugins is the [same as Winter](#winter-cms-standard-workflow), except that maintainers may commit changes to the `main` branch instead of the `develop` branch on an as-needed basis. As with Winter CMS, the primary mechanism for implementing changes is still recommended to be pull requests.

<a name="plugins-release-process"></a>
#### Release Process

Before a new version is released, the maintainer should ensure that all tasks assigned to the new version's milestone are completed and merged into `main`. Once this is done, the maintainer should update the `updates/version.yaml` file with the new version, and a one-line summary of the changes. Some example of these one-line changes can be found below:

- https://github.com/wintercms/wn-blog-plugin/blob/main/updates/version.yaml
- https://github.com/wintercms/wn-pages-plugin/blob/main/updates/version.yaml

If a migration is also included with this version, it should be appended as an array item below the update. See [the documentation](../plugin/updates#version-file) for examples of how the version update entries should be formatted.


Once this commit is done, the milestone for this version should be closed and a new Release should be created for plugin in GitHub. A release provides more detailed release notes for the version, and links back to the milestone and related PRs to give users of the plugin more context on changes to the plugin between version. The release also tags the repository with the version. The release name should be in the format `v<major>.<minor>.<point>` and the details of the release notes should contain the following, in order and if applicable:

- Breaking changes
- New features
- Other changes
- Bug fixes
- Translation updates

For each item in each section, it is preferable to either include a link to the PR that implemented the change, or alternatively, the commit if it was done directly into `main`.

At the bottom of the release notes, a link to the milestone should be provided to allow people to quickly see *all* changes implemented in that version.

For examples of Releases, see the following:

- https://github.com/wintercms/wn-blog-plugin/releases/tag/v1.3.4
- https://github.com/wintercms/wn-blog-plugin/releases/tag/v1.3.5
