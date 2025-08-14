#  JS8Call Developer and Contributor Guidelines

- Basic Overview of Project Structure
    The JS8Call project's main branch (sometimes called "master") is the branch to which PR's for all new features
    and enhancements should be submitted. It should be considered an unstable development branch, not suitable to
    build and run except for development purposes. It will likely be in a constant state of flux as new PR's are
    merged and tested.

    Stable release branches are prefixed with `release/` followed by the release version number, and these may also
    include release candidate versions of a pending stable production release
    
    The project maintainer core team is responsible for reviewing and merging PR's into the project. There is no set
    schedule for major or minor point releases. It is up to the project maintainer(s) to decide when and if main is
    worthy of a new release. However, the lifespan of a -rc (release candidate) should be generally considered to be
    no more than 30 days from the release of -rc1 for final debugging and testing before it goes to a stable production
    release.
    
#  How to submit a PR

-   Before starting work on the code make sure you checkout main on your local repo and git pull any changes that may
    have been merged since the last time you updated it. It is recommended to create a development branch from main
    with git checkout -b 'new_branch'. You can call it anything you wish, but the idea is always to keep your local
    main branch in sync with upstream and only make changes to your development branches. There may be other developers
    making changes to the code in the same files you are working on. Keeping your local main branch up-to-date with
    upstream will make it much easier to rebase your development branch on main before submitting a PR.
    
    Remember - it is the developer's responsibility to make sure your changes to the code build and run, and that all
    conflicts are resolved before submitting a PR.
    
    If your PR is a work-in-process where you may want peer code review or submissions from other contributors, label
    the PR with WIP - 'TITLE OF PR' so project maintainers can skip over it when merging other PR's without wasting
    time to determine if it is suitable for merge into the project.
    
#  Creating a clean commit history

-   If your PR contains a number of commits it is recommended to use interactive rebasing on your local branch prior
    to submitting a PR and pushing it to your github repo from which the PR will be created. For instance you may have
    a half dozen or more commits all related to the same changes to the code. Those commits should be squashed into one.
    Say there is six commits related to a bug fix or feature, you can combine them into one commit with:
    `git rebase -i HEAD~6` and change the pick column accordingly to squash them and combine the commit comments.
    
    The final step before creating a PR is to rebase your development branch on master, resolve any conflicts, build it
    and make sure it runs.
    
    If you are not familiar with rebasing, refer to the git documentation at
    https://git-scm.com/docs/git-rebase
    
    Remember - it is not the project maintainer(s) responsibility to build and test your PR. They expect you to submit
    good code that builds and runs. There may be bugs found in it later that need to be patched but the maintainer(s)
    do not have time to track down bad code that breaks main, or won't build and run.
    
#  Bug fixes

-   The whole purpose of a release candidate is final debugging by both end users and developers before it goes to a stable
    production release. If you submit a bug fix PR to a release candidate branch it is likely the same bug exists in main
    and a PR should be submitted to main as well. The same applies the other way around - if you are working on main and
    squash a bug, and the same code exists in a release candidate branch, a PR for the bug fix should be submitted to both
    branches.
    
    Under no circumstances submit PR's to a release candidate branch to get your latest and greatest feature into it, change
    libraries, etc.. New features go to main, only bug fixes are submitted to a release candidate.
    
    
    
    
    
    
    
    
