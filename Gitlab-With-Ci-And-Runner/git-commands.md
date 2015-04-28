###  Git is distributed versioning system and use sha1 40 hexadecimal character to track versioning… stage, commit and modified
```
INITIAL COMMANDS:

$ git config --global core.editor emacs  # set default editor in git 

$ git config —list     ## check you settings

$ git help config   ## getting help

$ git init   ##Initializing a Repository in an Existing Directory

$ git clone https://github.com/libgit2/libgit2   ###That creates a directory named “libgit2”, initializes a .git directory inside it,

$ git clone https://github.com/libgit2/libgit2 mylibgit  ###but the target di- rectory is called mylibgit.

$ git clone -o saurabh  https://github.com/libgit2/libgit2: change your remote name from origin to saurabh

$ git status  ##Checking the Status of Your Files  ### flags: -s 

$ git add README  ###Tracking New Files and parallely staging it and marking merge conflicted files

$ git diff    ##To see what you’ve changed but not yet staged # Flag: —staged : compares your staged changes to your last commit

It’s important to note that git diff by itself doesn’t show all changes made since your last commit – only changes that are still unstaged. 
This can be confusing, because if you’ve staged all of your changes, git diff will give you no output.

$ git commit -a -m 'added new benchmarks’

$ git rm PROJECTS.md

 git rm --cached README  ### to keep the file in your working tree but remove it from your staging area

$ git rm log/\*.log   ###Note the backslash (\) in front of the *. This is necessary because Git does its own filename expansion in add
			ition to your shell’s filename expansion.

$ git mv README.md README   ###If you want to rename a file in Git

$ git commit --amend: if you commit and then realize you forgot to stage the changes in a file you wanted to add to this commit,

$ git commit -m 'initial commit’

$ git add forgotten_file

$ git commit --amend

$ git reset HEAD CONTRIBUTING.md :Unstaging a Staged File

$ git checkout — CONTRIBUTING.md : Unmodifying a Modified File

$ git remote -v :Showing Your Remotes with url

$ git remote add [shortname] [url]:git remote add pb https://github.com/paulboone/ticgit: Adding Remote Repositories

$ git fetch [remote-name] :Fetching and Pulling from Your Remotes

$ git push origin master :Pushing to Your Remotes

$ git remote show origin :Inspecting a Remote

$ git remote rename pb paul : Renaming remotes

$ git remote rm paul : Removing the remotes
```

#### $ git log  
#### flags: 
``` 
-p: 	shows the difference introduced in each commit
-2:	limits the output to only the last two entries:
--stat: 	prints below each commit entry a list of modified files, how many files were changed, and how many lines in those files were ad
	ded and removed. It also puts a summary of the information at the end.
--pretty= oneline or format: “ %h - %an, %ar : %s: or short or full or fuller : This option changes the log output to formats other than the default.

				%H 		Commit hash

				%h 		Abbreviated commit hash

				%T 		Tree hash
				%t 		Abbreviated tree hash
				%P	 	Parent hashes
				%p 		Abbreviated parent hashes
				%an 		Author name
				%ae 		Author e-mail
				%ad 		Author date (format respects the --date=option) %ar Author date, relative
				%cn 		Committer name
				%ce 		Committer email
				%cd 		Committer date
				%cr 		Committer date, relative
				%s 		Subject

--graph: 	adds a nice little ASCII graph showing your branch and merge history:

--shortstat:	Display only the changed/insertions/deletions line from the --stat command.
--name-only :	Show the list of files modified after the commit information.
--name-status:	Show the list of files affected with added/modified/deleted
		information as well.
--abbrev-commit:	Show only the first few characters of the SHA-1 checksum nstead of all 40.
--relative-date: 	Display the date in a relative format (for example, “2 weeksago”) instead of using the full date format.
--since= 2.weeks  :	Limiting Log Output
-S function_name: 	Another really helpful filter is the -S option which takes a string and only
			shows the commits that introduced a change to the code that added or re-moved that string
 —after: 		Limit the commits to those made after the specified date.

 --until, —before: 	Limit the commits to those made before the specified date.

 —author:  		Only show commits in which the author entry matches the specified string.

 —committer: 		Only show commits in which the committer entry matches the specified string.

 —grep: 		Only show commits with a commit message con- taining the string

 The author is the person who originally wrote the work, whereas the committer is the person who last applied the work. So, if you send in a patch to a project and one of the core members applies the patch, both of you get credit – you as the author, and the core member as the committer.
```
 

#### Git Advance commands:

```
$ git tag:Listing Your Tags

$ git tag -l 'v1.8.5*’ :tags with a particular pattern.

## Git uses two main types of tags: lightweight and annotated.A lightweight tag is very much like a branch that doesn’t change – it’s just a
pointer to a specific commit. Annotated tags, however, are stored as full objects in the Git database.

They’re checksummed; contain the tagger name, e-mail, and date; have a tag- ging message; and can be signed and verified with GNU Privacy Guard (GPG). It’s generally recommended 
that you create annotated tags so you can have all this information; but if you want a temporary tag or for some reason don’t want to keep the other information, lightweight 
tags are available too.Typically people use this functionality to mark release points (v1.0 and so on)

$ git tag -a v1.4 -m 'my version 1.4’ :Annotated Tags

$ git tag v1.4-lw :Lightweight Tags

$ git tag -a v1.2 9fceb02 :Tagging Later To tag that commit, you spec- ify the commit checksum (or part of it) at the end of the command

$ git push origin v1.5 : push the tag to remote branch

$ git push origin —tags:This will transfer all of your tags to the remote server that are not already there.

By default, the git push command doesn’t transfer tags to remote servers. You will have to explicitly push tags to a shared server after 
you have created them. This process is just like sharing remote branches.

git checkout -b version2 v2.0.0 :  You can’t really check out a tag in Git, since they can’t be moved around. If you want to put a 
version of your repository in your working directory that looks like a specific tag, you can create a new branch at a specific tag.

$ git config --global alias.co checkout: Git Aliases



### A branch in Git is simply a lightweight movable pointer to one of the commits. 

$ git branch testing : create new branch but nor switch

$ git checkout testing : switch to new branch

$ git checkout -b iss53 :Switched to a new branch "iss53"

$ git branch -d iss53 :delete the branch:

$ git mergetool

$ git branch -v :To see the last commit on each branch,

$ git branch — merged :To see which branches are already merged into the branch you’re on,

$ git branch --no-merged:  To see all the branches that contain work you haven’t yet merged in 

$ git branch -d testing : delete the branch

### Long-Running Branches:The stable branches

### Topic Branches : temporary branches

Remote Branches:Remote branches are references (pointers) to the state of branches in your re- mote repositories.

$ git push origin serverfix: push into origin/branch

$ git config --global credential.helper cache.:you can set up a “credential cache

$ git checkout -b serverfix origin/serverfix

$ git checkout -b serverfix origin/serverfix:If you want your own serverfix branch that you can work on, you can base it off your remote branch:

$ git checkout --track origin/serverfix:Branch serverfix set up to track remote branch serverfix from origin.

$ git checkout -b sf origin/serverfix:Branch sf set up to track remote branch serverfix from origin.

$ git branch -u origin/serverfix:Branch serverfix set up to track remote branch serverfix from origin.

$ git branch -vv: to see what tracking branches you have set up

While the git fetch command will fetch down all the changes on the server that you don’t have yet, it will not modify your working directory at all. It will simply get 
the data for you and let you merge it yourself. However, there is a command called git pull which is essentially a git fetch immediately fol- lowed by a git merge in 
most cases. If you have a tracking branch set up as demonstrated in the last section, either by explicitly setting it or by having it created for you by the clone or 
checkout commands, git pull will look up what server and branch your current branch is tracking, fetch from that server and then try to merge in that remote branch.

$ git push origin --delete server fix :Deleting Remote Branches
```
#### Another set of advance commands:
```
Rebasing:

With the rebase command, you can take all the changes that were committed on one branch and replay them on another one.


$ git checkout experiment
$ git rebase master



$ git rebase --onto master server client:You can take the changes on client that aren’t on server (C8 and C9) and replay them on your master 
branch by using the --onto option of git rebase:

$ git rebase master server: Let’s say you decide to pull in your server branch as well. You can rebase the server branch onto the master branch without having to check 
it out first by running git rebase [basebranch] [topicbranch] – which checks out the topic branch (in this case, server) for you and replays it onto the base branch (master):

$ ssh user@git.example.com 

$ cd /opt/git/my_project.git 

$ git init --bare —shared 

You see how easy it is to take a Git repository, create a bare version, and place it on a server to which you and your collaborators have SSH access. Now you’re ready to 
collaborate on the same project.If a user SSHs into a server and has write access to the /opt/git/ my_project.git directory, they will also automatically have push access.

Git will automatically add group write permissions to a repository properly if you run the git init command with the --shared option. 

$ git request-pull origin/master my fork: The request-pull command takes the base branch into which you want your topic branch pulled and the Git repository URL you 
want them to pull from, and outputs a summary of all the changes you’re asking to be pulled in. For instance, if Jessica wants to send John a pull request, and she’s 
done two commits on the topic branch she just pushed up, she can run this:

$ git describe master :if you want to have a human-readable name to go with a commit, you can run git describe on that commit.

$ git archive master --prefix='project/' --format=zip > `git describe master`.zip: You now have a nice tarball and a zip archive of your project release that you
can upload to your website or e-mail to people. 

$ git show 1c002d :

$ git show HEAD^

$ git show HEAD~3

$ git add -i :Interactive Staging

## $ diff --git a/lib/simplegit.rb b/lib/simplegit.rb: It’s also possible for Git to stage certain parts of files and not the rest.
$ git stash: Now you want to switch branches, but you don’t want to commit what you’ve been working on yet; so you’ll stash the changes

$ git stash list:	To see which stashes you’ve stored

$ git stash apply:	If you want to apply one of the older stashes

$ git stash apply —index:	The changes to your files were reapplied, but the file you staged before wasn’t restaged. To do that, 
you must run the git stash apply command 
with a --index option to tell the command to try to reapply the staged changes. If you had run that instead, you’d have gotten back to 
your original position:

$ git stash --keep-index:	This tells Git to not stash anything that you’ve already staged with the git add command.

$ git stash -u: If you specify --include-untracked or -u, Git will also stash any untracked files you have created.

$ git stash —patch:Finally, if you specify the --patch flag, Git will not stash everything that is modified but will instead prompt you interactively which of the changes you 
would like to stash and which you would like to keep in your working directory.

$ git stash branch test changes: If you stash some work, leave it there for a while, and continue on the branch from which you stashed the work, you may have a problem reapplying 
the work. If the apply tries to modify a file that you’ve since modified, you’ll get a merge conflict and will have to try to resolve it. If you want an easier way to test 
the stashed changes again, you can run git stash branch, which creates a new branch for you, checks out the commit you were on when you stashed your work, reapplies your 
work there, and then drops the stash if it applies success- fully:

$ git clean -d -f -n:Assuming you do want to remove cruft files or clean your working directory, you can do so with git clean. To remove 
all the untracked files in your working directory, you can run git clean -f -d, which removes any files and also any subdirectories that 
become empty as a result. The -f means force or “really do this”.

If you ever want to see what it would do, you can run the command with the -n option, which means “do a dry run and tell me what you would have re- moved”.

$ git clean -x -i:This will run the clean command in an interactive mode. clean all files even which are in gitignore also.

Signing Your Work: Git is cryptographically secure, but it’s not foolproof. If you’re taking work from others on the internet and want to verify that commits 
are actually from a trusted source, Git has a few ways to sign and verify work using GPG. Skip it.

First of all, if you want to sign anything you need to get GPG configured and your personal key installed.

Git Grep: Git ships with a command called grep that allows you to easily search through any committed tree or the working directory for a string or regular expression. For these examples, we’ll look through the Git source code itself.

$ git grep -n gmtime_r: You can pass -n to print out the line numbers where Git has found matches.

$ git grep --count gmtime_r:Git summarize the output by just showing you which files matched and how many matches there were in each file with the --count option:

$ git grep --break --heading :Here we’ll also use the --break and --heading options which help split up the output into a more readable format.

$ git commit —amend:If you only want to modify your last commit message, It’s like a very small rebase – don’t amend your last com- mit if you’ve already pushed it.
That drops you into your text editor, which has your last commit message in it, ready for you to modify the message. When you save and close the editor, the editor writes a new commit containing that message and makes it your new last commit.

$ git rebase -i HEAD~3:For example, if you want to change the last three commit messages, or any of the commit messages in that group, you supply as an argument to 
git rebase -i the parent of the last commit you want to edit, which is HEAD~2^ or HEAD~3. It may be easier to remember the ~3 because you’re trying to edit the 
last three commits; but keep in mind that you’re actually designating four commits ago, the parent of the last commit you want to edit:

$ git rebase —continue:This command will apply the other two commits automatically, and then you’re done. If you change pick to edit on more lines, you can repeat these 
steps for each commit you change to edit. Each time, Git will stop, let you amend the commit, and continue when you’re finished.

	
$ git filter-branch --tree-filter 'rm -f passwords.txt’ HEAD: REMOVING A FILE FROM EVERY COMMIT$ git filter-branch --subdirectory-filter trunk HEAD: Suppose you’ve done an 
import from another source control system and have subdirectories that make no sense (trunk, tags, and so on). If you want to make the trunk subdirectory be the new 
project root for every commit, filter- branch can help you do that, too:
	
here’s a cheat-sheet for which commands affect which trees. The “HEAD” column reads “REF” if that command moves the reference (branch) that HEAD points to, and “HEAD” if 
it moves HEAD itself. Pay especial attention to the WDSafe? column – if it says NO, take a second to think before running that com- mand.
$ git merge abort: ABORTING A MERGE

$ git merge -Xignore-space-change whitespace: In this specific case, the conflicts are whitespace related. We know this because the case is simple, but it’s also 
pretty easy to tell in real cases when looking at the conflict because every line is removed on one side and added again on the other. By default, Git sees all of 
these lines as being changed, so it can’t merge the files.

 $ git log --oneline --left-right HEAD…MERGE_HEAD: To get a full list of all of the unique commits that were included in either branch involved in this merge, we can use the “triple dot” syntax that we learned in “Triple Dot”.

$ 
git log --oneline --left-right —merge: it will only show the commits in eitherside of the merge that touch a file that’s currently conflicted. 

$ git revert -m 1 HEAD: REVERSE THE COMMIT If moving the branch pointers around isn’t going to work 
for you, Git gives you the option of making a new commit which undoes all the changes from an ex- isting one. Git calls this operation a “revert”, and in this particular 
scenario, you’d invoke it like this:

$ git blame -L 12,22 simplegit.rb: If you track down a bug in your code and want to know when it was introduced and why, file annotation 
is often your best tool. It shows you what commit was the last to modify each line of any file. So, if you see that a method in your code is buggy, you can annotate 
the file with git blame to see when each line of the method was last edited and by whom. This example uses the -L option to limit the output to lines 12 through 
22:

Binary Search: Let’s say you just pushed out a release of your code to a production environ- ment, you’re getting bug reports about something that wasn’t happening 
in your development environment, and you can’t imagine why the code is doing that. You go back to your code, and it turns out you can reproduce the issue, but you can’t 
figure out what is going wrong. You can bisect the code to find out. First you run git bisect start to get things going, and then you use git bisect bad to tell the 
system that the current commit you’re on is bro- ken. Then, you must tell bisect when the last known good state was, using git bisect good [good_commit]: 

Git will go into your submodules and fetch and update for you.
$ git bisect start 
$ git bisect bad 
$ git bisect good v1.0git submodule update —remote: Bundling: The bundle com-
mand will package up everything that would normally be pushed over the wire with a git push command into a binary file that you can email to 
someone or put on a flash drive, then unbundle into another repository.

$ git bundle create repo.bundle HEAD master: if you want to send that repository to someone and you don’t have access toa repository to push to, or simply don’t want to 
set one up, you can bundle it with git bundle create. 

$ git clone repo.bundle repo: use that bundle

$ git replace 81a708d c6e1e95: you can simply call git replace with the commit you want to replace and then the commit you want to replace it with. So we want to replace the 
“fourth” commit in the master branch with the “fourth” commit in the project-history/master branch:$ git config --global credential.helper store --file ~/.my-credentials: OR $ git config --global credential.helper cache

$ git credential fill: to create the credential file 
$ git show-ref: 
here’s what a repository with tags looks like after a fresh clone:
```
#### Third List of Advence COmmands:
``` 
Plumbing and Porcelain:  

$ git cat-file -p master^{tree}: the most recent tree in a project may look something like this:
 
  ls -F1
$HEAD			 points to the branch you currently have checked out
config*			 contains your project-specific configuration options
description	 only used by the GitWeb program,
hooks/			contains your client- or server-side hook scripts
info/			keeps a global exclude file for ignored patterns that you don’t want to track in a .gitignore file
objects/	  stores all the content for your database
refs/			stores pointers into commit objects in that data

###rebasing, and resetting: porcelain

### Git’s plumbing: give us access to Git’s true internal representation of a project


git cat-file <type> <object-id>:  Display the specified object, where <type> is one of commit, tree, blob, or tag
git cat-file -t <object-id>: 	Output the type of the specified object.
git ls-tree <tree-id>: 		Display a pretty version of the specified tree object.
git gc: 			Perform a garbage collection on the object database.
git update-index [--add] <file>: Stage the specified file, using the optional --add flag to denote a new untracked file
git write-tree:			Generate a tree from the index and store it in the object database.Returns the ID of the new tree object.
git commit-tree <tree-id> -p <parent-id>:Create a new commit object from the given tree object and parent commit. Returns the ID of the new commit object.

### GIT REVERT:

'git revert' [--[no-]edit] [-n] [-m parent-number] [-s] [-S[<key-id>]] <commit>...
'git revert' --continue
'git revert' --quit
'git revert' —abort

###GIT CHERRY-PICK

git cherry-pick' [--edit] [-n] [-m parent-number] [-s] [-x] [--ff]
		  [-S[<key-id>]] <commit>...
'git cherry-pick' --continue
'git cherry-pick' --quit
'git cherry-pick' —abort
If you want to merge only a single commit but not total branch, use cherry-pick.

###GIT REBASE:

git rebase <new-base>:Move the current branch’s commits to the tip of <new-base>, which can be either a branch name or a commit ID.
git rebase -i <new-base> : interactive rebase
git commit —amend:Add staged changes to the most recent commit instead of creating a new one.
git rebase —continue:Continue a rebase after amending a commit.
git rebase —abort:Abandon the current interactive rebase and return the repository to its former state.
git merge --no-ff <branch-name>: Force a merge commit even if Git could do a fast-forward merge
You use git rebase if you want your changes in the feature branch to always the latest. And you use git merge if you want to reflect the true ordering of commits.

###SUBMODULES:

Including submodules as part of your Git development allows you to include other projects in your codebase, keeping their history separate but synchronized with 
yours. It’s a convenient way to solve the vendor library and dependency problems.

 the public repositories will be published under your home directory in ~/subtut/public. Let's create the four public submodule repositories first:

$ mkdir -p ~/subtut/private
$ mkdir -p ~/subtut/public
$ cd ~/subtut/private
$ for mod in a b c d; do
    mkdir $mod
    cd $mod
    git init
    echo "module $mod" > $mod.txt
    git add $mod.txt
    git commit -m "Initial commit, public module $mod"
    git clone --bare . ~/subtut/public/$mod.git
    git remote add origin ~/subtut/public/$mod.git
    git config branch.master.remote origin
    git config branch.master.merge refs/heads/master
    cd ..
done
Now create the public superproject; we won't actually add the submodules yet.
$ cd ~/subtut/private
$ mkdir super
$ cd super
$ git init
$ echo hi > super.txt
$ git add super.txt
$ git commit -m "Initial commit of empty superproject"
$ git clone --bare . ~/subtut/public/super.git
$ git remote add origin ~/subtut/public/super.git
$ git config branch.master.remote origin
$ git config branch.master.merge refs/heads/master

Check out the superproject somewhere private and add all the submodules (note: it's important to give an absolute path for submodules on the local filesystem).

$ cd ~/subtut/private
$ cd super
$ for mod in a b c d; do git submodule add ~/subtut/public/$mod.git $mod; done
$ ls -a
.  ..  .git  .gitmodules  a  b  c  d  super.txt
The "git submodule add" command does a couple of things:

It clones the submodule under the current directory and by default checks out the master branch.
It adds the submodule's clone path to the ".gitmodules" file and adds this file to the index, ready to be committed.
It adds the submodule's current commit ID to the index, ready to be committed.

$ cat .gitmodules
[submodule "a"]
        path = a
        url = /home/moses/subtut/public/a.git
[submodule "b"]
        path = b
        url = /home/moses/subtut/public/b.git
        ...
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   .gitmodules
#       new file:   a
#       new file:   b
#       new file:   c
#       new file:   d
#
```
