# Git and Github cheet sheet:
## In a nutshell, Git is a version control for project.
So we can use a distribuyed version control system: Each system has a full version of the project on it in which one of them can be the main servers
* Git let's you to save the changes of the each version of the project not the hole version.(by using a snapshot of each version)
* Git can be local version control system on your own pc --> But GitHub is the server for this control.
* How does Git WORKS??? 
1. Working Directory:\
The Working Directory is the physical folder on your computer where you can see all your project files. It's the place where you do all your work: you write code, add new files, and 	modify existing ones. When you make changes here, Git sees them as "untracked" or "modified." These changes are not yet part of your project's version history.
---------------------------
2. Staging Area:\
Also known as the "Index," the Staging Area is a temporary layer that sits between your Working Directory and the Git Repository. When you run the git add command, you're taking 		specific changes from your Working Directory and placing them in the Staging Area. This lets you prepare a snapshot of your changes before committing them. It's a key feature that allows you to group related changes together into a single, meaningful commit, even if you have other unrelated changes in your working folder.
---------------------------
3. .git Directory (Repository): \
The Git Repository is the core of your project. It's a hidden folder named .git that stores all of your project's history, including all the commits, branches, and tags. When you use the git commit command, Git takes everything you've placed in the Staging Area and permanently saves it as a new, permanent version of your project inside the repository. Each commit is like a permanent snapshot in time, and you can always go back to it.
---------------------------
## vs code 
in terminal visual studio code can open by using:
```bash
$ code
```
```bash
‍‍‍‍‍‍‍‍$ code ~/project directory/eg. main.py
```
or 
```bash
$ cd ~/project directory --> code . 
``` 
which opens code in current directory

## Setting Your Default Text Editor:

if you want vscode to be your main editor in git you can simply use:
```bash
$ git config --global core.editor "code --wait"
```
---------------------------
## Git Configs:
 Git configuration is done using git config and settings are stored in three levels:
 - System (--system): `/etc/gitconfig` – applies to all users and repositories.
 - Global (--global): `~/.gitconfig` or `~/.config/git/config` – specific to your user.
 - Local: `.git/config` in the repo – specific to that repository.
Each level overrides the previous one.
 
### Set your name and email (required for commits):
```bash
 $ git config --global user.name "John Doe"
 $ git config --global user.email "john@example.com"
```
### check settings:
```bash
 $ git config --list
```
### To see where a setting is defined: 
``` 
 $ git config --show-origin --get-regexp user.name
```
## Git Basics
### Getting a Git Repository
You can set up a Git repository in two ways:
#### 1. Initialize a New Repository
Go to your project directory and run:
```bash
$ git init
```
This creates a .git subdirectory containing all repository metadata. No files are tracked yet.
To start tracking files and make the first commit:
```bash
$ git add <file>          # Track specific file
$ git add *.c             # Add all .c files
$ git add LICENSE         # Add LICENSE file
$ git commit -m "Initial commit"
```
#### 2. Clone an Existing Repository
To copy a remote Git repository:
```bash
git clone https://github.com/libgit2/libgit2
```
This:
* Creates a local directory named libgit2
* Initializes .git
* Downloads all project history
* Checks out the latest version
To clone into a custom directory name:
```bash
$ git clone https://github.com/libgit2/libgit2 mylibgit
```
Supported Protocols:
* HTTPS: https://github.com/user/repo.git
* SSH: user@server:path/to/repo.git or ssh://user@server/path
* Git: git://server/path/to/repo.git

### Recording Changes in the Repository
Once you have a repo, you’ll work in three main areas:
* Working Directory: Your actual files.
* Staging Area (Index): Files marked for next commit.
* Repository: Committed snapshots.
Files can be:
* Tracked: Already in the
repo (unmodified, modified, or staged).
* Untracked: Not in the last commit and not staged.
#### Check File Status
Use:
```bash
$ git status
```
Example output:
```bash
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working tree clean
```
After adding a new file:
```bash
Untracked files:
  README
```
Use short status format:
```bash
$ git status -s
```
Output meaning:
| SYMBOL | MEANING         |
|--------|------------------|
| ??     | Untracked file   |
| A      | New file staged  |
| M      | Modified file    |
| D      | Deleted file     |
| R      | Renamed file     |

#### Tracking New Files
Stage a new (untracked) file:
```bash
$ git add README
```
Now it appears under:
```bash
Changes to be committed:
  new file: README
```
#### Staging Modified Files
If a tracked file (e.g., CONTRIBUTING.md) is edited:
```bash
$ git status
```
Shows:
```bash
Changes not staged for commit:
  modified: CONTRIBUTING.md
```
Stage it:
```bash
$ git add CONTRIBUTING.md
```
#### Ignoring Files with .gitignore
Create a .gitignore file to exclude unwanted files (logs, binaries, temp files, passwords(.env),...).
Example:
```bash
*.log           # Ignore all .log files
*.o             # Object files
*.a             # Archive files
*~              # Editor backup files (e.g., Emacs)
build/          # Entire build directory
/TODO           # Only root-level TODO
doc/*.txt       # Only .txt in doc/ (not subdirs)
doc/**/*.pdf    # All .pdf files in doc/ and subdirectories
!important.log  # Track this even if it matches a rule
```
Rules:
* Blank lines or lines starting with # are ignored.
* Use * (any chars), ? (single char), [abc] (char class).
* Use /** / for recursive matching (e.g., a/**/z matches a/b/c/z).
* Start with / to match root only.
* End with / to match directories only.
* Use ! to negate a pattern.
###  Viewing Changes
Use git diff to see exact changes, not just filenames.

#### Compare Working Directory vs Staging Area
```bash
$ git diff
```
Shows unstaged changes (what you've edited bun not "git add" ed).

#### Compare Staged Changes vs Last Commit
```bash
$ git diff --staged
```
or
```bash
$ git diff --cached
```
Shows what will be included in the next commit.
#### VScode as difftool
To make VS Code your default “everything”, first you need to ensure you can run VS Code from the command-line.

Then, run the command `git config --global --edit` to edit the global config, and add the following:
```bash
[core]
  editor = code --wait
[diff]
  tool = vscode
[difftool "vscode"]
  cmd = code --wait --diff $LOCAL $REMOTE
```
If you already have [core] section, just add the [diff] and [difftool] sections to it.
### Committing Changes
Once files are staged:
```bash
$ git commit
```
This opens your default editor (set via git config --global core.editor) to write a message.

The editor shows:

* Commented git status output
* Lines starting with # are ignored
* An empty message cancels the commit
Or, commit with a message inline:
```bash
$ git commit -m "Fix login bug"
```
Output example:
```bash
[master 463dc4f] Fix login bug
 2 files changed, 5 insertions(+)
 create mode 100644 README
```
### Skip the Staging Area
To stage and commit all tracked and modified files at once:
```bash
$ git commit -a -m "Commit message"
```
This is equivalent to:
```bash
$ git add <all tracked files>
$ git commit -m "Commit message"
```
### Removing Files
To remove a file from Git and disk:
```bash
$ git rm filename
```
Then commit:
``` bash
$ git commit -m "Remove old cinfig"
```
If the file is modified/uncommitted, use -f:
```bash
$ git rm -f filename 
```
To keep the file on disk but stop tracking it:
```bash
$ git rm --cached filename
```
Useful for accidentally added files(e.g. logs, binaries)

#### Remove Multiple Files
Use wildcards (escape * to prevent shell expansion):
```bash
$ git rm log/\*.log     # All .log files in log/
$ git rm \*~            # All files ending in ~
```
### Moving (Renaming) Files
Git doesn't track moves, but detects them.
#### Rename file 
```bash
$ git mv old.txt new.txt
```
After this, git status shows:
```
renamed: old.txt -> new.txt
```
This equivalent to:
```bash
$ mv old.txt new.txt
$ git rm old.txt
$ git add new.txt
```
## Viewing the Commit History
After making or cloning commits, you’ll want to review your project’s history. The main tool for this is ‍‍‍`git log`.

By default `git log` shows:
* Commits in reverse chronological order (newest first)
* Full SHA-1 checksum
* Author name and email
* Commit date
* Commit message
Example:
```bash
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    Change version number
```
#### Useful Formatting Options:

| Option | Purpose |
|--------|---------|
| `-p`, `--patch` | Shows the **patch (diff)** introduced in each commit. |
| `-2`, `-n` | Limits output to last **n commits** (e.g., `-2` = last 2). |
| `--stat` | Shows **statistics**: files changed, insertions/deletions. |
| `--shortstat` | Only shows summary line from `--stat`. |
| `--name-only` | Lists **files modified** after commit info. |
| `--name-status` | Shows files with **A**dded / **M**odified / **D**eleted status. |
| `--abbrev-commit` | Shows **shortened** commit hash (e.g., `ca82a6d`). |
| `--relative-date` (`--date=relative`) | Shows dates like "2 weeks ago". |
| `--graph` | Adds **ASCII graph** showing branch and merge history. |
| `--pretty=<format>` | Customizes output format. |

#### Common `--pretty` Formats:
* `oneline`: One line per commit: <hash> <message>
* `short`: Author and message only
* `full`: Author + committer
* `fuller`: Includes full dates
* `format`:"...": Define custom format
Example: Custom Format
```bash 
$ git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : Change version number
```
#### ASCII Graph Example
```bash 
$ git log --pretty=format:"%h %s" --graph
* 2d3acf9 Ignore errors from SIGCHLD on trap
*  5e3ee11 Merge branch 'master' of https://github.com/dustin/grit.git
|\
| * 420eac9 Add method for getting current branch
* | 30e367c Timeout code and tests
|/
* d6016bc Require time for xmlschema
```
Great for visualizing branching and merging.
####  Limiting Output (Filtering Commits)
| Option | Description |
|--------|-------------|
| `-<n>` | Show last **n** commits (e.g., `-3` shows last 3 commits). |
| `--since`, `--after` | Show commits made after a specific date (e.g., `2 weeks ago`, `2008-10-01`). |
| `--until`, `--before` | Show commits made before a specific date. |
| `--author="John"` | Only show commits where the author matches the given string (case-sensitive). |
| `--committer="Jane"` | Filter commits by committer name. |
| `--grep="fix"` | Show only commits whose message contains the specified keyword. |
| `-S "function_name"` | Show commits that added or removed lines containing the given string (known as "pickaxe"). |
| `--no-merges` | Exclude merge commits from the output. |
| `-- path/to/file` | Only show commits that modified the specified file or directory. |
#### Advanced Filter Example
Find non-merge commits by Junio Hamano in October 2008 that modified test files:
```bash
$ git log --pretty="%h - %s" \
          --author='Junio C Hamano' \
          --since="2008-10-01" \
          --before="2008-11-01" \
          --no-merges \
          -- t/
```
Output:
```bash 
5610e3b - Fix testcase failure when extended attributes are in use
acd3b9e - Enhance hold_lock_file_for_{update,append}() API
...
```
#### Summary: Most Useful Commands
```bash 
# Show last 3 commits with diffs
$ git log -p -3

# Show stats per commit
$ git log --stat

# Compact one-line history
$ git log --oneline

# Custom format with short hash and message
$ git log --pretty=format:"%h - %an: %s"

# Show branch graph
$ git log --graph --oneline

# Find commits modifying a specific function
$ git log -S "initialize_system"

# Only show changes to a specific file
$ git log -- README.md

# Search by author and date range
$ git log --author="Ali" --since="last week" --grep="bug"
```
## Undoing things 
Git provides powerful tools to undo changes at various stages — but some undos are irreversible, especially if commits haven’t been pushed or saved. Use these commands carefully to avoid losing work.
### Amending the Last Commit 
if you committed too early, forgot to add a file, or made a typo in the message:
```bash
$ git commit --amend
```
* Uses the current staging area to replace the last commit.
* Opens the editor to edit the commit message (pre-filled with the previous one).
* Results in one new commit — the old one is removed from history
For example:
```bash
$ git commit -m "Initial commit"
$ git add forgotten_file
$ git commit --amend 
```
### Unstaging a Staged File
If you accidentally stage a file with `git add`, you can unstage it without losing changes.
```bash
git restore --staged <file>
```
Example:
```bash
$ git add *
$ git status
# ... shows both files staged

$ git restore --staged CONTRIBUTING.md
```
### Discarding Changes in a Modified File
f you want to discard local changes and revert a file to its last committed/staged version:
```bash
git restore <file>
```
Example:
```bash
$ git restore CONTRIBUTING.md
```
#### Key Notes
Committed changes are safe: Even deleted or amended commits can often be recovered (see Git’s `reflog` and Data Recovery tools).

## Working with Remotes
To collaborate in Git, you need to manage remote repositories — versions of your project hosted elsewhere (on the internet or even on your local machine). Remotes let you push, pull, and share changes with others.

### Showing your Remotes 
List configured remotes:
```bash 
$ git remote
```
#### Show remotes with their URLs:
```bash 
$ git remote -v
```
* `origin` is the default name for the remote when you clone a repo.
* You can have multiple remotes (e.g., for different collaborators).

### Adding a Remote 
Add a new remote with a short name:
```bash
$ git remote add <shortname> <url>
```
Example:
```bash
$ git remote add pb https://github.com/paulboone/ticgit
```
Now you can use `pb` instead of the full URL.

### Fetching and Pulling from Remotes
Fetch (download data without merging):
```bash
$ git fetch <remote>
```
* Downloads all new data from the remote.
* Does not automatically merge into your work.
* Remote branches become accessible (e.g., `pb/master`).

#### Pull (fetch + merge):
```bash 
$ git pull <remote> <branch>
```
* Automatically fetches and merges the remote branch into your current branch.
* By default, `git clone` sets up your local branch to track the remote one, so `git pull` works out of the box.

#### Pushing to a Remote:
Push your commits to a remote branch:
```bash
$ git push <remote> <branch>
```
Example:
```bash
$ git push origin master
```
### Inspecting a Remote
Get detailed info about a remote:
```bash
$ git remote show <remote>
```
Example:
```bash
$ git remote show origin
```
--------------------
### Renaming and Removing Remotes
Rename a remote:
```bash
$ git remote rename <old> <new>
```
Example:
```bash
$ git remote rename pb paul
```
Automatically updates remote-tracking branches (e.g., pb/master → paul/master). 

#### Remove a remote:
```bash
git remote remove <name>
# or
git remote rm <name>
```
Removes:
* The remote reference
* All remote-tracking branches
* Associated configuration
------------------
## Tags
Tags in Git are used to mark specific points in history — typically for releases (e.g., `v1.0`, `v2.0`). Unlike branches, tags are immutable and don’t change.
### Listing Tags:
```bash
$ git tag
```
Search for tags matching a pattern (requires `-l` or `--list`):
```bash 
$ git tag -l "v1.8.5*"
```
Example output:
```bash 
v1.8.5
v1.8.5-rc1
v1.8.5.1
```
### Types of Tags:
#### 1. Annotated Tags
* Stored as full Git objects.
* Include: tagger name, email, date, message, and checksum.
* Can be signed with GPG for security.
* Recommended for official releases.
Create an annotated tag:
```bash 
$ git tag -a v1.4 -m "my version 1.4"
```
View tag data:
```
$ git show v1.4
```
Shows tag info + the commit it points to.

#### 2. Lightweight Tags
* Just a pointer to a commit (no extra metadata).
* Useful for temporary or personal tags.
Create a lightweight tag:
```bash
git tag v1.4-lw
```
`git show` only shows the commit, not tag details.

### Tagging After the Fact
You can tag any past commit using its hash:
```bash
$ git tag -a v1.2 9fceb02
```
This tags the commit `9fceb02` (e.g., “Update rakefile”) as `v1.2`.

### Sharing Tags
By default, git push does not send tags to remote repositories.
Push a specific tag:
```bash
$ git push origin v1.5
```
Push all local tags:
```bash
$ git push origin --tags
```
This sends both annotated and lightweight tags. 
To push only annotated tags, use: 
```bash
$ git push origin --follow-tags
```
### Deleting Tags
#### Delete locally:
```bash
$ git tag -d v1.4-lw
```
#### Delete from remote:
Two ways:
```bash
$ git push origin :refs/tags/v1.4-lw
```
or (more intuitive):
```bash
$ git push origin --delete v1.4-lw
``` 
### Checking Out Tags
To view the project at a tagged version:
```bash
$ git checkout v2.0.0
``` 
This puts you in "detached HEAD" state:
* You can view code and make changes.
* But any new commits won’t belong to a branch and may be lost unless saved.
#### Safe way: Create a branch from the tag
```bash
$ git checkout -b version2 v2.0.0
```
Now you’re on a new branch `version2` based on the `v2.0.0` tag, and can commit safely.

## Git Aliases
Git allows you to create custom shortcuts (aliases) for commands using `git config`. This makes frequently used commands faster and easier to type.
### How to Set Up Aliases
Use:
```bash
$ git config --global alias.<shortcut> <full-command>
```
#### Common Examples
```bash
$ git config --global alias.co checkout
$ git config --global alias.br  branch
$ git config --global alias.ci  commit
$ git config --global alias.st  status
```
Now:
`git co` = `git checkout`
`git br = git branch`
`git ci = git commit`
`git st = git status`
### Useful Custom Aliases
Improve clarity or add missing commands:
```bash
$ git config --global alias.unstage 'reset HEAD --'
```
Now these are equivalent:
```bash
$ git unstage fileA
$ git reset HEAD -- fileA
```
Another helpful alias:
```bash
$ git config --global alias.last 'log -1 HEAD'
```
View the most recent commit:
```bash
$ git last
```
### Running External Commands
To run non-Git (external) commands, start the alias with `!`:
```bash 
$ git config --global alias.visual '!gitk'
```
Now `git visual` runs the external GUI tool `gitk`.

## What is a Branch in Git?
* A branch in Git is a lightweight, movable pointer to a commit.
* The default branch is called `master` (but it’s not special—it’s just the default).
* Each commit points to its parent(s), forming a chain of snapshots (not diffs).
* Branching is fast and cheap because Git only stores a pointer (41 bytes) per branch.
### How Git Stores Data
* Git saves data as snapshots, not changesets.
* When you commit:
1. Files are stored as blobs (using SHA-1 hashes).
2. Directories are stored as tree objects.
3. A commit object is created with metadata and a pointer to the root tree and parent commit(s).
### Creating a Branch
* Use: `git branch <branch-name>`
* Creates a new pointer to the current commit.
* Does not switch to the new branch.
Example:
```bash
$ git branch testing
```
### Switching Branches
* Use: git checkout <branch-name> (or git switch in Git 2.23+)
* Moves the HEAD pointer to the specified branch.
* Updates your working directory to match that branch’s snapshot.
Example:
```bash
$ git checkout testing
```
Or (with newer Git):
```bash
$ git switch testing
```
⚠️ Note: Switching branches changes your files! Git reverts the working directory to the state of the target branch. 

### Making Commits on a Branch
When you commit, the current branch’s pointer moves forward.
* Other branches remain unchanged.
* This creates divergent history between branches.
Viewing Branch History
* To see all branches and their positions:
```bash
$ git log --oneline --decorate --graph --all
```
#### This shows:
* Commit history
* Branch pointers
* Divergence and merge points
### Create & Switch in One Step
Old way:
```bash
$ git checkout -b new-branch
```
#### New way (Git 2.23+):
```bash
$ git switch -c new-branch
```
### Why Git Branching is Powerful
* Lightweight: Creating branches is instant (just a file with a hash).
* Encourages frequent branching and merging.
* Supports flexible workflows (e.g., feature branches, bug fixes).
* Safe experimentation: switch between versions easily.
### Key concept
| Concept      | Description |
|--------------|-------------|
| HEAD         | A pointer to the currently checked-out branch. |
| Branch       | A pointer to a commit; moves forward on new commits. |
| Divergence   | Happens when different changes are made on separate branches. |
| Merge Base   | Automatically determined when merging branches later. |

## Basic Branching and Merging
### Typical Branching Workflow
* Work on `master` branch.
* Create a feature branch for a new task (e.g., `iss53`).
* Make changes and commit in the feature branch.
* Get interrupted by a critical bug/hotfix.
* Switch back to `master`, create a `hotfix` branch.
* Fix the issue, test, then merge into master and deploy.
* Delete the `hotfix` branch.
* Return to the original feature branch and continue work.
* When ready, merge the feature into master.
### Creating and Switching Branches
Create and switch to a new branch:
```bash
$ git checkout -b iss53
```
Equivalent to:
```bash
$ git branch iss53
$ git checkout iss53
```
Or use modern syntax (Git 2.23+):
```bash
$ git switch -c iss53
```
### Hotfix Workflow
When a critical fix is needed:
```bash
$ git checkout master
$ git checkout -b hotfix
$ vim index.html
$ git commit -a -m "Fix broken email address"
```
After testing, merge hotfix into master:
```bash
$ git checkout master
$ git merge hotfix
```
Output shows: `Fast-forward` (if no divergence).
🔁 Fast-forward Merge: When the target branch is behind, Git simply moves the pointer forward—no merge commit is created. 

#### Delete the hotfix branch:
```bash
$ git branch -d hotfix
```
### Merging a Feature Branch
When feature is complete:
```bash
$ git checkout master
$ git merge iss53
```
This creates a merge commit if history has diverged.
A merge commit has two parents: one from master, one from iss53.
🧩 Three-way merge: Git uses three snapshots: 

* The common ancestor
* The tip of master
* The tip of iss53
After merging, delete the feature branch:
```bash
$ git branch -d iss53
```
### Merge Conflicts
Occur when the same part of a file is changed in both branches.
Example:
```bash
$ git merge iss53
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
Check status:
```bash
$ git status
# Unmerged paths: both modified: index.html
```
Conflict markers in file:
```bash
<<<<<<< HEAD
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">please contact us at support@github.com</div>
>>>>>>> iss53
```
To resolve:
* Edit the file manually (or use a merge tool).
* Remove conflict markers.
* Save and stage:
```bash
$ git add index.html
```
Complete the merge:
```bash
$ git commit
```
💡 You can use `git mergetool` for a graphical interface (e.g., `meld`, `vimdiff`, etc.). 

Finalize merge with a commit message (default: `Merge branch 'iss53'`). You can edit it to explain the resolution.
### Key concept 
| Concept           | Description |
|-------------------|-------------|
| **Fast-forward merge** | Git moves the branch pointer forward without creating a new commit. Happens when no divergence exists. |
| **Three-way merge**   | Used when histories diverge. Combines changes from two branches and their common ancestor. |
| **Merge commit**      | A commit with **two parents**, created during non-fast-forward merges. |
| **Merge conflict**    | Occurs when the same file/line is changed differently in both branches. Must be resolved manually. |
| **git status**        | Shows which files have conflicts during a merge. |
| **git add**           | After resolving a conflict, staging the file marks it as resolved. |
| **git mergetool**     | Launches a GUI tool to help resolve merge conflicts. |


## Git Branch Management
The `git branch` command is more than just a tool for creating and deleting branches — it’s a powerful utility for managing and inspecting your branch structure.

### Viewing Branches
* Running `git branch` with no arguments lists all local branches.
* The current branch is marked with an asterisk (`*`), indicating it’s the one checked out (i.e., where `HEAD` points).
* Use `git branch -v` (verbose) to see the last commit message on each branch.
* `git branch --merged`: shows branches that have already been merged into the current branch. These are generally safe to delete using `git branch -d`.
* git branch --no-merged: shows branches containing work that hasn’t yet been merged. Attempting to delete such branches with `git branch -d` will fail unless you use `git branch -D` (force delete).
Tip: By default, --merged and --no-merged compare against your current branch. You can specify another branch as a reference:
```bash
git branch --no-merged main
```
This shows branches not yet merged into main, even if you’re not currently on main. 

### Renaming a Local and Remote Branch
If you have a branch with a bad name (e.g., `bad-branch-name`) and want to rename it to something better (e.g., `corrected-branch-name`), follow these steps:
#### Rename the branch locally:
```bash
git branch --move bad-branch-name corrected-branch-name
```
#### Push the new branch to the remote and set upstream tracking:
```bash
git push --set-upstream origin corrected-branch-name
```
#### Delete the old branch name from the remote:
```bash
git push origin --delete bad-branch-name
```
Now, the branch is renamed both locally and on the remote (e.g., GitHub, GitLab). Other team members will need to update their local repos accordingly if they had the old branch.
Caution: Avoid renaming branches that are actively being used by others unless coordinated. This can cause confusion and workflow disruptions. 

### Changing the Default Branch Name (e.g., master → main)
Many projects now rename the default branch from `master` to `main` (or similar) for inclusivity and clarity. Here's how to do it safely:

#### Rename the local branch:
```bash
git branch --move master main
```
#### Push the new branch to the remote:
```bash
git push --set-upstream origin main
```
At this point:
* Your local `master` branch is gone (renamed to `main`).
* The `main` branch exists on the remote.
* But the old `master` branch still exists on the remote and may still be set as the default.
### Post-Rename Tasks
Before deleting the old master branch, complete these critical steps:

#### Update repository settings on the hosting platform (GitHub/GitLab/etc.):

* Change the default branch to `main`.
* Update pull request targets, branch protection rules, and merge strategies.
#### Update automation and integrations:
* CI/CD pipelines (e.g., GitHub Actions, Jenkins, GitLab CI)
* Build scripts, deployment tools, and release workflows
* Any configuration files that reference `master`
#### Update documentation and code:
* READMEs, contribution guides, and internal docs
* Scripts, configuration files, or comments that mention `master`
#### Notify collaborators:

* Ask team members to rename their local master branch:
```bash
git branch --move master main
```
And reconfigure remote tracking:
```bash
git fetch origin
git branch --set-upstream-to=origin/main main
``` 
#### Close or redirect pull requests targeting `master` to point to `main`.

### Final Step: Delete the Old Branch from Remote
Once everything is updated and verified, remove the old `master` branch from the remote:
```bash
git push origin --delete master
```
Now, `main` is fully established as the new default branch.

⚠️ Warning: Renaming `master` (or `main`) affects everything connected to the repo. Always communicate changes in advance and double-check all integrations. 

### Conclusion
Effective branch management in Git includes:

* Listing, inspecting, and cleaning up merged/unmerged branches.
* Safely renaming branches when needed.
* Thoughtfully transitioning default branch names across teams and systems.
By using commands like `git branch -v`, `--merged`, `--no-merged`, `--move`, and proper remote syncing, you can keep your repository organized, clean, and collaborative.

## Git Branching Workflows
Git’s lightweight branching enables flexible and powerful development workflows.

### Long-Running Branches
Some teams use long-lived branches to manage different stability levels:

* `master`/`main`: only stable, production-ready code.
* `develop`/`next`: integration branch for features; less stable.
* `pu` (proposed updates): for experimental changes in large projects.
Changes flow upward: topic branches → `pu` → `develop` → `master`. This creates a progressive-stability model, where code "graduates" to more stable branches after testing.

### Topic Branches
Short-lived branches for specific features, fixes, or experiments (e.g., `iss91`, `dumbidea`).
#### Benefits:

* Isolate work and avoid cluttering main branches.
* Switch contexts quickly.
* Merge when ready, discard if not.
* Easy code review and clean history.
You can create, merge, and delete many topic branches daily — Git makes this fast and simple.

### Local Workflow
All branching and merging happens locally. No server interaction is needed until you’re ready to share.

### Final Note
There’s no one-size-fits-all workflow. Common models like Git Flow or GitHub Flow build on these ideas. Choose the strategy that fits your team and project — and explore more in the Distributed Git chapter.

## Git Branching - Remote Branches
Remote branches are a core part of collaborative Git workflows. They allow you to track the state of branches in remote repositories, synchronize your work with teammates, and manage distributed development efficiently.

What Are Remote References?
Remote references are pointers to branches, tags, and other references in your remote repositories. You can view them directly using:
```bash
git ls-remote <remote>
```
or get a more readable summary with:
```bash
git remote show <remote>
```
However, the most practical way to interact with remote branches is through 
-------------
### remote-tracking branches.
Remote-tracking branches are `local references` that reflect the state of branches on a remote repository the last time you communicated with it.

* They are named in the format: <remote>/<branch>
(e.g., `origin/master`, `origin/develop`, `teamone/feature`).
* These branches cannot be moved manually — Git updates them automatically during network operations like `fetch` or `pull`.
* Think of them as bookmarks: they remind you where the remote branches were the last time you fetched.
#### Example: When you run
```bash
git clone https://github.com/user/project
```
### How Fetching Works
When others push changes to the remote, your local remote-tracking branches don’t update automatically — you need to fetch.

Use:
```bash
git fetch origin
```
This:

* Connects to the `origin` remote.
* Downloads any new commits or branches you don’t have.
* Updates your remote-tracking branches (like `origin/master`, `origin/iss53`).
* Does not change your working directory or current branch.
⚠️ Your local branches (e.g., `master`) stay where they are until you merge or rebase. 

You can also fetch from multiple remotes:
```bash
git remote add teamone https://git.team1.company.com/project
git fetch teamone
```
This adds a new remote and creates remote-tracking branches like `teamone/master`.

### Pushing Your Branches
By default, Git does not automatically push your local branches. You must explicitly push the branches you want to share.

To push a local branch (e.g., `serverfix`) to the remote:
```bash
git push origin serverfix
```
This:

* Creates a `serverfix` branch on the remote (if it doesn’t exist).
* Sets your local `origin/serverfix` remote-tracking branch.
Allows others to fetch and collaborate on it.

You can also push to a differently named remote branch:
```bash
git push origin serverfix:awesomebranch
```
This pushes your serverfix to a remote branch called awesomebranch.

💡 This is useful for proposing changes or following team naming conventions. 

### Working with Remote Branches: Checkout and Tracking
When you fetch a remote branch (e.g., `origin/serverfix`), you don’t get a local editable branch — only the remote-tracking reference.

To create a local branch based on it:
```bash
git checkout -b serverfix origin/serverfix
```
This:

* Creates a new local serverfix branch.
* Sets it to start at the same commit as origin/serverfix.
* Sets up tracking: now serverfix is linked to origin/serverfix.
Git makes this easier with shorthand options.

#### Shorthand 1: `--track`
```bash
git checkout --track origin/serverfix
```
Same as above — creates and tracks the branch.

#### Shorthand 2: Automatic Tracking
If a local branch doesn’t exist and there’s only one matching remote branch:
```bash
git checkout serverfix
```
Git automatically creates the branch and sets up tracking from origin/serverfix.

✅ This is safe and commonly used. 

#### Custom Local Name
To use a different local name:
```bash
git checkout -b sf origin/serverfix
```
Now `sf` tracks `origin/serverfix`.

### Set Upstream Later
If you already have a local branch and want to link it to a remote:
```bash
git branch -u origin/serverfix
```
This sets the upstream (tracking) relationship.

📌 Use @{upstream} or @{u} as a shortcut for the tracked branch: 
```bash
git merge @{u}   # same as git merge origin/serverfix
```
### Viewing Tracking Information
To see which branches are tracking which remotes:
```bash
git branch -vv
```
Output example:
```bash 
  iss53     7e424c3 [origin/iss53: ahead 2] Add forgotten brackets
  master    1ae2a45 [origin/master] Deploy index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] This should do it
  testing   5ea463a Try something new
```
This shows:

* `iss53`: tracking `origin/iss53`, has 2 unpushed commits.
* `master`: tracking `origin/master`, fully up to date.
* `serverfix`: tracking `teamone/server-fix-good`, 3 commits ahead, 1 behind (someone else pushed).
* testing: not tracking any remote.
⚠️ These stats are based on cached data. To get accurate results: 
```bash
git fetch --all
git branch -vv
```
### Pulling Changes
`git pull` is a convenience command that combines:
* 1. `git fetch` (download changes)
* 2. `git merge` (merge the remote branch into your current branch)
If you’re on a tracking branch (e.g., `serverfix` tracking `origin/serverfix`), then:
```bash
git pull
```
automatically:
* Fetches from `origin`.
* Merges `origin/serverfix` into your `serverfix` branch.
####  Equivalent to:
`git fetch origin` + `git merge origin/serverfix` 
⚠️ `git pull` can sometimes lead to unexpected merges. For more control, many prefer to `fetch` first, then `merge` manually. 

### Deleting Remote Branches
When a feature is done and merged, you can delete the remote branch to clean up.

Use:
```bash
git push origin --delete serverfix
```
Output:
```bash
 - [deleted]         serverfix
```
This:

* Removes the branch pointer from the remote.
* Does not delete the commits immediately — Git keeps them for a while (until garbage collection).

Others will see the deletion the next time they run `git fetch` or `git remote prune origin`.

## Git Rebasing
In Git, there are two primary methods for integrating changes from one branch into another: merging and rebasing. While both achieve the same end result in terms of code content, they differ significantly in how they shape the project’s history.
-------------
### 1. What is Rebasing?
Rebasing is the process of moving or combining a sequence of commits onto a new base commit. It allows you to take the changes introduced in one branch and replay them on top of another branch, resulting in a linear and cleaner project history.

Basic Example
Suppose you have:

* A master branch ending at commit C3.
* An experiment branch that diverged at C2 and has a new commit C4.
Instead of merging, you can rebase:
```bash
git checkout experiment
git rebase master
```
This:

* Finds the common ancestor (`C2`).
* Extracts the changes from `C4` as a patch.
* Resets experiment to `C3` (the tip of `master`).
* Applies the patch from `C4` on top, creating a new commit `C4'`.
Now `C4'` contains the same changes as `C4`, but sits directly on top of `master`.

You can then fast-forward master:
```bash
git checkout master
git merge experiment  # Fast-forward merge
```
Result: A clean, linear history with no merge commit.

### 2. Why Use Rebasing?
#### Advantages
* Cleaner history: No unnecessary merge commits when branches were developed in parallel.
* Easier to read logs: The history appears as if all changes were made sequentially.
* Ideal for feature branches: Before merging a feature into main or develop, rebasing keeps integration smooth.
* Helps contribution workflows: When contributing to public projects, rebasing your work onto the latest `origin/main` ensures your pull request applies cleanly.
Note: The final code snapshot after rebasing is identical to that after merging — only the history structure differs. 

### 3. Advanced Rebase: git rebase --onto
You can rebase a branch onto a different base, even skipping intermediate branches.

Use Case
Imagine this structure:
```bash 
master ← base
server ← branched from master, has some commits
client ← branched from server, has its own commits
More commits added to server
```
Now, you want to integrate only the client changes into master, but not the server changes (e.g., they’re not ready yet).

Use:
```bash
git rebase --onto master server client
```
This means:

"Take the commits from client that are not in server, and replay them directly onto master." 

After this:

client now points to commits based on master.
You can safely merge client into master via fast-forward.
Then, later, rebase server onto master:
```bash
git rebase master server
```
This rebases server (and checks it out automatically), placing its work on top of `master`.

Finally, merge both into master and delete the temporary branches:
```bash
git branch -d client
git branch -d server
```
 Final result: A clean, linear history with all changes integrated logically.

### 4. The Dangers of Rebasing: The Golden Rule
Never rebase commits that have been pushed to a public repository and that others may have based their work on. 

Why?
* Rebasing rewrites history: It creates new commits (with new SHA-1 hashes) that replace the old ones.
* If others have already pulled your original commits, their work is based on the old history.
* When you force-push the rebased version (git push --force), it breaks collaboration.
#### Example of Problems
* Alice pushes commits C1, C2 to a shared repo.
* Bob pulls them and builds new commits (C3, C4) on top.
* Alice rebases her work (e.g., to clean history) and force-pushes.
* Bob now faces a conflict: his C3 and C4 are based on now-missing commits.
When Bob pulls, Git sees two divergent lines and creates a merge commit that includes both versions, leading to:

* Duplicate commits with same message/date.
* Confusion about what code is current.
* Risk of re-introducing "abandoned" changes.
### 5. Recovering from Rebased Public History
If someone force-pushes rebased commits, you can minimize damage using:

`git pull --rebase`
```bash
git pull --rebase
```
This tells Git:

"Get the new upstream changes, then reapply my local commits on top — don’t merge." 

Git uses patch IDs (checksums of the actual changes) to detect if your commits are duplicates of already-rebased ones. If so, it skips them.

This avoids duplicate commits and keeps your history clean.

Make It Default
To always rebase when pulling:
```bash 
git config --global pull.rebase true
```
Or use:
```bash
git config --global rebase.autoStash true  # Automatically stash unstaged changes during rebase
```
### 6. Rebase vs Merge: Philosophical Differences
The choice between rebase and merge often reflects team values about project history.
| **Merge**                                      | **Rebase**                                     |
|------------------------------------------------|------------------------------------------------|
| Preserves exact history of how and when work happened. | Creates a curated story of development.         |
| Honors collaboration and parallel work.        | Presents a clean, linear progression.          |
| Merge commits show integration points.         | No extra merge commits; looks like one continuous effort. |
| Best for shared/public branches.               | Best for local/private branches.               |

#### Analogy: 

Merging is like publishing a raw diary.
Rebasing is like editing a book before publishing.
### 7. Best Practices & Recommendations
#### Do rebase when:

* Working on private branches.
* Preparing a feature for a pull request.
* Cleaning up your own commit history before sharing.
* You haven’t pushed your commits yet.
#### Don’t rebase when:

The branch has been pushed and shared.
Others have based their work on your commits.
You’re working in a team environment with strict history preservation policies.
Hybrid Strategy (Best of Both Worlds):

Use rebase locally to keep your feature branches clean.
Use merge (or merge --no-ff) when integrating into shared branches like main or develop.
This gives you a clean feature history and preserves integration context in the main line.


---------------
## i will complete this part later
---------------
