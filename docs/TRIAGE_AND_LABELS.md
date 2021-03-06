# Triage Process and GitHub Labels for Angular

This document describes how the Angular team uses labels and milestones to triage issues on github.
The basic idea of the process is that caretaker only assigns a component (`comp: *`) label.
The owner of the component is then responsible for the secondary / component-level triage.


## Label Types

### Components

The caretaker should be able to determine which component the issue belongs to.
The components have a clear piece of source code associated with it within the `/packages/` folder of this repo.

* `comp: docs-infra` - the angular.io application
* `comp: animations`
* `comp: bazel` - @angular/bazel rules
* `comp: benchpress`
* `comp: common` - this includes core components / pipes
* `comp: common/http` - this includes core components / pipes
* `comp: core & compiler` - because core, compiler, compiler-cli and
  browser-platforms are very intertwined, we will be treating them as one
* `comp: ivy` - a subset of core representing the new Ivy renderer.
* `comp: elements`
* `comp: forms`
* `comp: http`
* `comp: i18n`
* `comp: language-service`
* `comp: metadata-extractor`
* `comp: router`
* `comp: server`
* `comp: service-worker`
* `comp: testing`
* `comp: upgrade/dynamic`
* `comp: upgrade/static`
* `comp: web-worker`
* `comp: zones`

There are few components which are cross-cutting.
They don't have a clear location in the source tree.
We will treat them as a component even thought no specific source tree is associated with them.

* `comp: build & ci` - build and CI infrastructure for the angular/angular repo
* `comp: docs` - documentation, including API docs, guides, tutorial
* `comp: packaging` - packaging format of @angular/* npm packages
* `comp: performance`
* `comp: security`

Sometimes, especially in the case of cross-cutting issues or PRs, these PRs or issues belong to multiple components.
In these cases, all applicable component labels should be used to triage the issue or PR.


### Type

What kind of problem is this?

* `type: RFC / discussion / question`
* `type: bug`
* `type: docs`
* `type: feature`
* `type: performance`
* `type: refactor`


## Caretaker Triage Process (Primary Triage)

It is the caretaker's responsibility to assign `comp:  *` to each new issue as they come in.

If it's obvious that the issue or PR is related to a release regression, the caretaker is also responsible for assigning the `severity(5): regression` label to make the issue or PR highly visible.

The primary triage should be done on a daily basis so that the issues become available for secondary triage without much of delay.

The reason why we limit the responsibility of the caretaker to this one label is that it is likely that without domain knowledge the caretaker could mislabel issues or lack knowledge of duplicate issues.


## Component's owner Triage Process

The component owner is responsible for assigning one of the labels from each of these categories:

- `type: *`
- `frequency: *`
- `severity: *`

We've adopted the issue categorization based on [user pain](http://www.lostgarden.com/2008/05/improving-bug-triage-with-user-pain.html) used by AngularJS. In this system every issue is assigned frequency and severity based on which the total user pain score is calculated.

Following is the definition of various frequency and severity levels:

1. `freq(score): *` ??? How often does this issue come up? How many developers does this affect?
    * low (1) - obscure issue affecting a handful of developers
    * moderate (2) - impacts auxiliary usage patterns, only small number of applications are affected
    * high (3) - impacts primary usage patterns, affecting most Angular apps
    * critical (4) - impacts all Angular apps
1. `severity(score): *` - How bad is the issue?
    * inconvenience (1) - causes ugly/boilerplate code in apps
    * confusing (2) - unexpected or inconsistent behavior; hard-to-debug
    * broken expected use (3) - it's hard or impossible for a developer using Angular to accomplish something that Angular should be able to do
    * memory leak (4)
    * regression (5) - functionality that used to work no longer works in a new release due to an unintentional change
    * security issue (6)


These criteria are then used to calculate a "user pain" score as follows:

`pain = severity ?? frequency`

This score can then be used to estimate the impact of the issue which helps with prioritization.


## Triaging PRs

Triaging PRs is the same as triaging issues, except that PRs have additional label categories that should be used to signal their state.

Every triaged PR must have a `pr_action` label assigned to it:

* `PR action: review` - work is complete and comment is needed from the reviewers.
* `PR action: cleanup` - more work is needed from the author.
* `PR action: discuss` - discussion is needed, to be led by the author.
* `PR action: merge` - the PR is ready to be merged by the caretaker.

In addition, PRs can have the following states:

* `PR state: WIP` - PR is experimental or rapidly changing. Not ready for review or triage.
* `PR state: blocked` - PR is blocked on an issue or other PR. Not ready for review or triage or merge.


## PR Target

In our git workflow, we merge changes either to the `master` branch, the active patch branch (e.g. `5.0.x`), or to both.

The decision about the target must be done by the PR author and/or reviewer.
This decision is then honored when the PR is being merged by the caretaker.

To communicate the target we use the following labels:

* `PR target: master & patch`: the PR should me merged into the master branch and cherry-picked into the most recent patch branch. All PRs with fixes, docs and refactorings should use this target.
* `PR target: master-only`: the PR should be merged only into the `master` branch. All PRs with new features, API changes or high-risk changes should use this target.
* `PR target: patch-only`: the PR should be merged only into the most recent patch branch (e.g. 5.0.x). This target is useful if a `master & patch` PR can't be cleanly cherry-picked into the stable branch and a new PR is needed.
* `PR target: LTS-only`: the PR should be merged only into the active LTS branch(es). Only security and critical fixes are allowed in these branches. Always send a new PR targeting just the LTS branch and request review approval from @IgorMinar.
* `PR target: TBD`: the target is yet to be determined.

If a PR is missing the "PR target: *" label, or if the label is set to "TBD" when the PR is sent to the caretaker, the caretaker should reject the PR and request the appropriate target label to be applied before the PR is merged.


## PR Approvals

Before a PR can be merged it must be approved by the appropriate reviewer(s).

To ensure that there right people review each change, we configured [PullApprove bot](https://about.pullapprove.com/) via (`.pullapprove.yaml`) to provide aggregate approval state via the GitHub PR Status API.

Note that approved state does not mean a PR is ready to be merged.
For example, a reviewer might approve the PR but request a minor tweak that doesn't need further review, e.g., a rebase or small uncontroversial change.
Only the `PR action: merge` label means that the PR is ready for merging.


## Special Labels

### `cla: yes`, `cla: no`
Managed by googlebot.
Indicates whether a PR has a CLA on file for its author(s).
Only issues with `cla:yes` should be merged into master.

### `aio: preview`
Applying this label to a PR makes the angular.io preview available regardless of the author. [More info](../aio/aio-builds-setup/docs/overview--security-model.md)
