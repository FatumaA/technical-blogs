---
title: "Notes on Git, GitHub and GitHub Desktop"
datePublished: Wed Oct 25 2023 02:43:47 GMT+0000 (Coordinated Universal Time)
cuid: clo55is2k00010ajy2xcc9k89
slug: notes-on-git-github-and-github-desktop
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1698188283499/5d19e48b-333f-4e10-8d0e-35da0fba67da.png
tags: github, git, collaboration, github-desktop

---

I recently got into a bit of trouble with Git while making some open-source contributions. Sweat happened 😅 but thankfully a whole lot of learning and relearning. These are a few notes to my future self (and you) on some Git basics and beyond for the next time Git gets us.

## What is Git?

First, the basics... or what even is Git? Git is an open-source version control system for your code. It is a command line tool meaning it is accessed via terminal applications. Git needs to be installed before it is available on your computer. You can find installation instructions [here](https://git-scm.com/download).

It is used to manage code files and the changes made to the files over time. Think of it like a tracker that tracks your file changes over time and keeps the file history; enabling you to roll back to a previous state of the file as needed.

Git's distributed nature makes it a crucial tool for enabling seamless collaboration by enabling multiple individuals to work on the same codebase. This makes it particularly needed in team-based software development projects.

### Common Git and Git-Related Terms/Commands

* **Pull -** The action of grabbing changes from a remote repository that don't exist on your local repository and adding them to it.  
    Example - `git pull origin master`.
    
* **Push -** This applies your local commits to the remote repository.
    
    Example - `git push origin feature-branch`.
    
* **Clone -** This copies a remote repository into your system.
    
    Example: `git clone` [`https://github.com/user/repo.git`](https://github.com/user/repo.git).
    
* **Fork -** Copies a remote repository that is not yours into your GitHub account. This copy will no longer be in sync with the origin repository and will need to be periodically synced to keep it up to date with its origin.
    
* **Contribution -** Changes made to a repository that is not yours. Could be changes that fix a bug or clarify documentation.
    
* **Issue -** Refers to the GitHub tool that allows developers to report bugs, request features and otherwise collaborate when working on a remote project.
    
* **Origin -** Refers to the remote repository from which your local repository was cloned. The origin of your local repo would be the remote repo and the origin of the remote repo would be its parent from which it was forked.
    
    Example - `git remote add origin <repo URL>`.
    
* **Commit -** Asking Git to add your changes to the repository, creating a snapshot of the repo.
    
    Example - `git commit -m "Added new feature"`.
    
* **Commit Message -** Description of the changes made in a commit.
    
    Example - `git commit -m "fix: what you fixed"`.
    
* **Commit Hash -** Unique identifier for a commit.
    
    Example: `3a879bebf837dc30d05f52d44f1b9402e135aeb97`.
    
* **Squash Commits -** Combines multiple commits into one commit.
    
    Example: `git rebase -i HEAD~3` to squash the last 3 commits into one.
    
* **Branch -** A separate copy of your project that you can make changes to without affecting the original code while staying within the same repo.
    
* **Main -** Default branch of a Git repository. May find places where it is called "master" instead.
    
* **Upstream**: Refers to the remote repository from which you originally forked your remote repository.
    
* **Remote**: The repository on GitHub that is linked to your local repository. Common remotes include "origin" and "upstream."
    
* **Pull Requests -** An ask that your proposed changes to a repository be added to the main branch.
    
* **Draft Pull Request -** A pull request that is not ready. Perhaps you are making requested changes or asking for help.
    
* **Code Review. -** A process of inspecting changes for quality and any problems for the changes are accepted.
    
* **Stash -** Temporarily saved changes that you are not ready to commit.
    
    Example: `git stash save "Work in progress"`.
    
* **Merge -** Integrates changes from one branch into another.
    
    Example: `git merge feature-branch`.
    
* **Diff -** Comparing two changes to find the difference.
    
    Example: `git diff commitA commitB`.
    
* **Conflicts -** This is when there are changes in a file between two different branches or commits that are clashing. Requiring to be resolved manually.
    
* **Head -** A pointer to the latest commit in a branch.
    
    Example: `git reset --hard HEAD~2` to reset to two commits back from the current HEAD.
    
* **Rebase -** Reapplies commits on top of another branch. An alternative to merge.
    
    Example: `git rebase main` to rebase the current branch onto the main branch.
    
* **Revert -** Undo a commit by creating a new commit that undoes the changes introduced by the commit you want to undo.
    
    Example: `git revert commit-hash` to revert a specific commit by its hash.
    
* **Reset -** Allows you to move the HEAD and current branch pointer to a specific past commit causing you to rewind/alter the history on that branch.
    
    Example: `git revert commit-hash` to revert a specific commit by its hash.
    
* **Cherry-pick -** Moves specific commits from one branch to another.
    
    Example: `git cherry-pick commit-hash` moves a commit from one branch to the current branch.
    

### Rebase and Interactive Rebase

Git rebase enables you to integrate changes from one branch into another. it does this by combining commits into a singular new commit. Typically, this would be between a feature branch and the main branch.

Interactive rebase is an advanced git feature that allows you to modify commits and adjust the branch history. It is a tool to reach out if your branch history needs cleaning and if you need fine-grained control over each of the commits during the rebasing process.

You start an interactive rebase session by typing the following command into the terminal:  
`git rebase -i <branch you want to rebase on>`

This will open an editor with all commands available to you written. Something like this:

```bash
pick fb4d36a fix: add uid
pick e322893 add many to many relation
pick 1875625 added about

# Rebase c9b002e..1875625 onto c9b002e (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
~                                                                                                                      
~                                                                                                                      
~                                                                                                                      
~      
```

You can modify any particular commit as needed and then save changes. You can also stop the rebase by running `git rebase --abort`. In case you have conflicts, you can run `git rebase --continue` after you have resolved the conflicts so that the rebase continues and completes.

It is best to do this, if at all before you push changes to a remote repository.

### Common Git Tasks

* **How to make a new branch?**  
    `git branch <branch name>`
    
* **How to switch between different branches?**  
    `git checkout <branch name>` for branches that are available in your local repository and `git checkout -b <branch name>` if the branch is in the remote but not in your local setup.
    
* **How to undo a commit?**  
    `git revert <commit-hash-of-commit you want to remove>`
    
* **How to move your changes from one branch to another?**  
    Run git `cherry-pick <commit-hash-of-correct-commit>` while on the branch you want to bring the changes to. This picks the commits by hash and applies the changes to the current branch.
    

## What is GitHub?

GitHub is a platform that hosts Git repositories remotely. It offers a web-based user interface that makes it easier to start using Git. When you create a code repository on GitHub, it initiates a corresponding Git repository in the background. This makes getting started with and understanding git a lot more straightforward.  
  
In addition, GitHub allows for extra collaboration and takes full advantage of Git's distributed nature. It does so by providing a point of reference, allowing its users to grab a copy of any given public repository and more easily coordinate on the same.

***Note: While GitHub is very popular, it is not the only such application. There are alternatives including BitBucket.***

## Git, GitHub and Collaboration

So how exactly do Git and GitHub allow for multiple developers in multiple places to coordinate on the same code repository? When you create a code repository, you will either have done so locally on your machine using Git or remotely using GitHub.

In the case of a local repo, Git directly gives you the ability to branch off your main repo essentially creating a local copy of your repo. You can make changes to this branch without affecting the original state of your repo. You can keep this new branch of your repo, delete it or bring any changes made back into the original state of the repo (thus, changing it).

  
In the case of a remote repo, GitHub would have already initialized a Git repo for you. You can grab a copy of your remote repo and set it up locally. You can then make changes and use all git features. These local changes won't change the state of your remote repo unless you explicitly command git to synchronize your changes.  
  
Where ease of collaboration shines through is when multiple developers have a copy of one remote repository. They can each make changes without affecting each other and when ready they can sy nchronize their changes. Git and GitHub will notify each developer in the case that their local copy is in conflict with the shared remote repository and will guide them on how best to solve the conflicts. This ensures collaboration without the developers overwriting each other.

## What about Github Desktop?

[GitHub Desktop](https://desktop.github.com/) is a cross-platform application that provides a graphical user interface (GUI) for interacting with Git via GitHub. It is best suited for use with repositories that are or will be hosted on GitHub.

It makes it much easier to effectively use Git as it visually shows Git's main functionality, is intuitive to use and increases its accessibility for newer developers and developers who prefer not to use the command line.

## Command Line Vs. GitHub Desktop

Strictly speaking, you don't need GitHub Desktop. But there is no shame in using a GUI if it makes your life easier. For this, I say use the one you are most comfortable with because you will be more productive that way.  
  
Besides, who says you can't use both? 😅.

## On Git and VS Code

*This section only applies to developers who use* [*Visual Studio Code*](https://code.visualstudio.com/download) *as their code editor.*

VS Code comes with Git support. You do not need to do anything in particular to use Git other than having it installed on your system. However, you can connect vs code to your GitHub account directly. This brings GitHub Desktop capabilities into vs code so you won't need to switch views as much. This increases productivity and might reduce your use of GitHub Desktop.  
  
You can find details of how to integrate vs code with GitHub and more on that [here](https://vscode.github.com/).

## Best practices?

Here is a list of things to keep in mind when collaborating with others on GitHub. Most especially for repositories you do not own or ones you do not have certain privileges with:

* Always read the README and contributing guidelines
    
* Respect any rules of conduct outlined
    
* Always keep your forks' main up to date with the parent repository's main
    
* Always branch off the main branch before you make changes
    
* Always ask what the expectation is if unsure
    
* Keep your commits small and frequent
    
* Use clear and concise commit messages
    
* Attempt to solve any conflicts before you open a pull request
    
* Ensure processes - e.g. testing, building - work as expected with your changes before making a pull request
    
* Describe clearly what your pull request does. Use screenshots and screen recordings where necessary
    
* Ensure no similar issues have been filed before you open a new issue
    
* Use relevant tags for new issues
    
* Ideally, file an issue before you open a pull request or start work if no relevant issue exists
    
* Ideally, link your pull request to its corresponding issue in its description
    
* Be egoless. Accept and offer corrections/guidance graciously
    

## Resources

Here is a list of resources that might be useful:

* [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
    
* [Post on Atomic Commits](https://www.aleksandrhovhannisyan.com/blog/atomic-git-commits/)
    
* [Git Best Practices](https://about.gitlab.com/topics/version-control/version-control-best-practices/)
    
* [Egoless Programming](https://blog.codinghorror.com/the-ten-commandments-of-egoless-programming/)
    
* [Git Rebase and Interactive Docs](https://git-scm.com/docs/git-rebase)
    
* [Post on Rewriting Git History](https://thoughtbot.com/blog/git-interactive-rebase-squash-amend-rewriting-history)