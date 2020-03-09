# Git Guidance

## Installation

[Install Git](https://git-scm.com/downloads) and follow the [First-Time Git Setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)

## Tools

Use a shell/terminal to work with Git commands instead of relying on [GUI clients](https://git-scm.com/downloads/guis/).

If you're working on Windows, [posh-git](https://github.com/dahlbyk/posh-git) is a great PowerShell environment for Git. Another option is to use [Git bash for Windows](http://www.techoism.com/how-to-install-git-bash-on-windows/). On Linux/Mac, install git and use your favorite shell/terminal.

## Working with repositories

### Contributing to an existing repository

When working on an existing project, `git clone` the repository and ensure you understand the team's branch, merge and release strategy (e.g. through the projects [CONTRIBUTING.md file](https://blog.github.com/2012-09-17-contributing-guidelines/)).

### Creating a new repository

If you create a new repository, agree on your branch, merge and release strategies upfront and document them in your [CONTRIBUTING.md](https://blog.github.com/2012-09-17-contributing-guidelines/). Also lock the default branches and define who can approve and merge Pull Requests into your default branches.

#### Required files in default branch for public repositories

A public repository needs to have the following files in the root directory of the default branch:

* a [LICENSE](Templates/LICENSE) file
* a [README.md](Templates/README.md) file
* a [CONTRIBUTING.md](Templates/CONTRIBUTING.md) file
* reference the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)

To start to contribute by creating your own branch, or fork the repository, and commit often - make your commit comments useful to others by including the *WHAT* and *WHY* (instead of the *HOW*).

## Commit history

Agree if you want a linear or non-linear commit history. There are pros and cons to both approaches:

* Pro linear: [A tidy, linear Git history](https://www.bitsnbites.eu/a-tidy-linear-git-history/)
* Con linear: [Why you should stop using Git rebase](https://medium.com/@fredrikmorken/why-you-should-stop-using-git-rebase-5552bee4fed1)

### Approach for non-linear commit history

Merging `topic` into `master`

```md
  A---B---C topic
 /         \
D---E---F---G---H master

git fetch origin
git checkout master
git merge topic
```

### Two approaches to achieve a linear commit history

#### Rebase topic branch before merging into master

Before merging `topic` into `master`, we rebase `topic` with the   :

```bash
          A---B---C topic
         /         \
D---E---F-----------G---H master

git fetch origin
git rebase master topic
git checkout master
git merge topic
```

#### Rebase topic branch before squash merge into master

[Squash merging](https://docs.microsoft.com/en-us/azure/devops/repos/git/merging-with-squash?view=azure-devops) is a merge option that allows you to condense the Git history of topic branches when you complete a pull request. Instead of adding each commit on `topic` to the history of `master`, a squash merge takes all the file changes and adds them to a single new commit on `master`.

```bash
          A---B---C topic
         /
D---E---F-----------G---H master

Create a PR topic --> master in Azure DevOps and approve using the squash merge option
```

### Naming branches

Let's use the following naming conventions for branches:

* personal branches: `user/your_alias/feature_name`
* feature branches for staging (testing, integration, …): `staging/feature_name`
* release branches: `release/release_name`

### Release Strategy/Versioning

Please see [versioning](versioning/readme.md).

### Working with secrets (such as storage keys)

The best way to avoid leaking secrets is to store them in local/private files and exclude these from  git tracking with a [.gitignore](https://git-scm.com/docs/gitignore) file.
E.g. the following pattern will exclude all files with the extension `.private.config`:

```bash
# remove private configuration
*.private.config
```

For more details on proper management of credentials and secrets in source control, and handling an accidental commit of secrets to source control, please refer to the [Secrets Management](../continuous-deployment/secrets-management/readme.md) document which has further information, split by language as well.

As an extra security measure, apply [credential scanning](../continuous-integration/credential-scanning/recipes/detect-secrets.md) in your CI/CD pipeline

### Reverting and deleting commits

To "undo" a commit, run the following two commands: `git revert` and `git reset`. `git revert` creates a new commit that undoes commits while `git reset` allows to delete commits entirely from the commit history.

> If you have committed secrets/keys, `git reset` will remove them from the commit history!

To **delete** the latest commit use `HEAD~`:

```bash
git reset --hard HEAD~1
```

To delete commits back to a specific commit, use the respective commit id:

```bash
git reset --hard <sha1-commit-id>
```

after you deleted the unwanted commits, push using `force`:

```bash
git push origin HEAD --force
```

### Multi service development and deployments

Submodules can be useful in more complex deployment and/or development scenarios

Adding a submodule to your repo

```bash
git submodule add -b master <your_submodule>
```

Initialize and pull a repo with submodules:

```bash
git submodule init
git submodule update --init --remote
git submodule foreach git checkout master
git submodule foreach git pull origin
```

### Stashing

`git stash` is super handy if you have un-committed changes in your working directory but you want to work on a different branch. You can run `git stash` and save the un-committed work and reverts back to the HEAD commit. You can retrieve the saved changes by running `git stash pop`:

```bash
git stash
…
git stash pop
```

Or you can move the current state into a new branch:

```bash
git stash branch <new_branch_to_save_changes>
```
