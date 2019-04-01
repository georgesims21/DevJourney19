# Git and version control

--------------------

## Types of version control

---

### Central VCS (SVN)

![1553797207278](/home/george/.config/Typora/typora-user-images/1553797207278.png)

This is located in one place, making changes to things then putting it back in the same place after making changes. *It's an issue if the person cannot get access to the repository*, so if offline etc you can only see the files you have checked out and not the most recent versions.

### Distributed VCS (GIT)

![1553801939807](/home/george/.config/Typora/typora-user-images/1553801939807.png)

Everybody has a local repo. The local repo has all the info about the files within the repo from the last time you synced. This can be viewed as each developer has a backup of the whole system.

---

## Local

---

### Global configuration variables

#### Name and email

These values will be your credentials for all push/pull/commits on GIT. 

To define these:

```bash
git config --global user.name "<First Name> <Last Name>"
git config --global user.email "<Email address>"
```

#### Check the values

```bash
git config --list
```

### Basic useage

#### Initializing a repository

To track an existing code base by git:

```bash
git init
```

Which will track all the files within the current directory.

#### Stop tracking file(s)

```bash
rm -rf .git
```

As the .git file contains all info about the files and their history.

#### Check status

```bash
git status
```

Will provide you with useful information regarding the current branch, tracked and untracked files.

#### Ignoring file(s)

Some files like preferences etc do not need to be pushed with the code, to ignore certain files they must be in the *.gitignore* file. First create such file in the directory:

```bash
touch .gitignore
```

Within the file, it is possible to ignore singular files, or even wild cards, meaning ignoring all files with a certain file type.

```bash
# Within the .gitignore file
.project 	# Ignore singular file
*.py 		# Ignore all .py files (wildcard)
```

Once this .gitignore is defined and saved, running the command *git status* again will show that they are now *invisible* to git and aren't picked up anymore.

#### Staging file(s)

![1553848413162](/home/george/.config/Typora/typora-user-images/1553848413162.png)

The three stages are listed above. Running *git status* will show us files that are in the working directory. With the staging area being sent to the directory on the staged files.

The useful thing about staging files is if lots of files have been modified by you over the course of the day, the staging area can be used to push multiple commits instead of all the changed files at once.

```bash
git add -A 	# Add all files in dir to staging area
git add .project hello.py	# Add files singularly
```

#### Unstage file(s)

```bash
git reset	# Remove all files from staging area
git reset .project	# Remove files singularly
```

#### Committing

```bash
git commit -m "<Enter commit message>" # -m is a commit message
```

#### Logs

```bash
git log
```

Gives us a list of all commits from this repository, with their corresponding hash number, user and message.

### Reverting changes

*These changes should only be made locally. This is due to these changes altering the .git history which can cause issues when editing a remote repository.*

#### Delete recent changes before commit

```bash
git checkout <filename>
```

Will delete any changes made to this file since the last commit.

#### Change most recent commit message 

```bash
git commit --amend -m "<new message>"
```

#### Show file changes in commit

```bash
git log --stat
```

#### Add file to previous commit

```bash
touch <filename>
git add <filename>
git commit --amend # Puts created file to last commit
# Also brings up editor allowing for extra changes
```

#### Commit changes to alternate branch

```bash
git log
# Copy hash of the commit you want to copy (6 chars is enough)
git checkout <branch name> # Branch to move commit to
git cherry-pick <copied hash number>
# Checkout to original branch
git log
# Copy hash of the commit before the branch you wish to delete
git reset --hard <most recent copied hash number>
```

*There are 3 types of reset: --soft, --mixed and --hard. Soft will not delete any changes before the reset point, and they will stay in the staging area. Mixed will also avoid deleting changes but they will be 'untracked' instead of 'staged*''. Finally hard will revert everything and lose any changes.*

#### Remove untracked files

```bash
git clean -df
```

*-d will remove any directories and -f any files.*

#### Retrieving 'lost' files

**This will only be allowed before 30 days or so from the deletion.**

```bash
git reflog # Shows order of commits from when last referenced
# Copy commit hash value you wish to get back
git checkout <copied hash value>
git log # You will see changes made in this commit
git branch <backup branch name> # Create branch to save to

# To confirm the changes were saved
git checkout master
git branch # Does the <backup branch name> exist?
git checkout <backup branch name>
git log # Compare hash value of most recent to one copied earlier
```



---

## Remote

---

### Basic usage

#### Cloning

```bash
git clone <url>	# The url must end with .git, so github or local
git clone <url> . # Clones <url> files into current dir, no new dir
```

#### Fetching info

```bash
git remote -v 	# Shows remote repo info
git branch -a	# Shows all branches of repo
```

#### See changes made to files

```bash
git diff
```

#### Pull changes

This command is important when working with other developers as it pulls the most recent commit from the remote to the local repository.

```bash
git pull origin master # Origin is remote name, master is branch
```

#### Push changes

This pushes all changes which were committed to the remote repo.

```bash
git push origin master
```

### Reverting changes

These commands will not change the git history or cause an error when pushing to a remote repo. *New commits will be made on top of the existing ones which undo the unwanted changes*.

#### Undo particular commit

*If you wanted to undo a commit that may have been checked out by other people:*

```bash
git log
# Copy hash number
git revert <copied hash number>

git log
# Will tell you the commit has been reverted, yet the old one is still visible on the list below this

# To see changes between the two
git diff <first hash number> <second hash number>
```



---

## Common work-flow

---

The usual way to work with git on a day-to-day basis is to create branches for each feature you wish to implement.

### Branches

#### Create

```bash
git branch <branch name>
```

#### List all branches

```bash
git branch -a # The current working branch is highlighted in green
```

#### Change working branch

```bash
git checkout <branch name>
```

#### Committing

This has no effect on the main local branch or the remote repo.

```bash
git commit -m "<commit message>"
```

#### Pushing

```bash
git push -u origin <branch name>
```

*Here the -u represents an initial command which links the branch to the origin (in this case), meaning the next time you wish to push/pull to/from this branch you can simply type:*

```bash
git pull
git push
```

#### Merging

```bash
git checkout master # Change working branch to master
git pull origin master
git branch --merged # Will say the branches merged so far
git merge <branch name> # Merge into master
git push origin master # Push to master on remote repo
```

#### Deleting

```bash
git branch --merged # Check if <branch name> merged successfully
git branch -d <branch name> # Delete the branch locally
git branch -a # Check if it still exists on remote repo
git push origin --delete <branch name> # Delete from remote too
```

 