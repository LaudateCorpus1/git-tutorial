Say you are writing a report; instead of renaming different versions of files `report.tex`, `report_final.tex`, `report_FINAL.tex`, `report_FINALFINAL.tex`, `report_FINAL_abcdef.tex`, ad infinitum, version control systems allow you to save the entire history of a file as a series of staged changes, often called commits or revisions. This may not seem so useful for a linear process such as a single person writing a document, but for the non-linear process of software development, version control is a must. Every single serious software project exists under some form of version control, almost by definition. Learning effective version control is a vital skill, not just for careers in the tech industry, but also for producing sustainable software and thus reproducible science.
TODO: Add paragraph about git's internal data structure

### What is a commit?
A commit (a.k.a. revision, changeset) is a set of file modifications grouped under the same user-provided descriptive tag and randomly-generated hash, providing a snapshot in time of the entire repository
- How often should I commit?
    * Changes to commit can be as fine or coarse grained as necessary, depending on personal preference
    * When writing a new code, the first commit might not occur until
- What makes a good commit message?
    * Simply a short description of the changes made. For example, good messages include "fixed typo in example.cpp", "added diffraction plotting function", "got ODE solver working" and "attempted second example", but "fixed example", "added function", "code now working" and "end of first practical" are less good. If you want to write a long description, write a short description as the first line, then use new lines to add detail.
- Can I edit previous commits?
- Can I undo a commit?


## Examples

You may find these examples easier to follow online, where text can be copied more easily: <www.github.com/ml-evs/part2-computing-git-tutorial>.

### Basic usage: single-user repositories

#### Setup

First things first, check that you have Git installed on the machine you're using for these tutorials. On Linux/\*nix (including Windows Subsystem for Linux), at the command-line:

```
$ git --version
git version 2.20.1
```

If you are using Windows directly, you need Git installed to reach this shell.

The next thing to do is register who you are to Git:
```
$ git config --global user.name "<your_name_here>"
$ git config --global user.email "<your_email_here>"
```

#### A simple example

In this example, we will make a local Git repository, add some files to it, commit them, make some changes, then commit the changes. Very exciting. First, lets make a file with some text in it, by reciting Moby Dick from memory...

```
$ echo "Call me Moby. Some years ago - never mind how long precisely - having little or no money in my purse, and nothing particular to interest me on shore, I thought I would sail about a little and see the water part of the world." >> moby.txt
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	moby.txt

nothing added to commit but untracked files present (use "git add" to track)
```

As you can see, although the file is in the correct folder, it is not being tracked by Git until we `add` it.

```
$ git add moby.txt
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   moby.txt
```
We can now write our first commit, bearing in mind the [advice](#what-is-a-commit) on what makes a good commit message (try to write your own).
```
$ git commit
```
Another quick `git status` will show that there is now `nothing to commit, working tree clean`. We're now ready to add the next sentence, which will let us look at a `diff` between the current state of our history (called `HEAD`). Git `diff` will open the diff in `less`, press Q to quit.
```
$ echo "The morning was so damp and misty that it was only with great difficulty that the day succeeded in breaking." >> moby.txt
$ git diff
diff --git a/moby.txt b/moby.txt
index c256b41..6a234d6 100644
--- a/moby.txt
+++ b/moby.txt
@@ -1 +1,2 @@
 Call me Moby. Some years ago - never mind how long precisely - having little or no money in my purse, and nothing particular to interest me on shore, I thought I would sail about a little and see the water part of the world.
+The morning was so damp and misty that it was only with great difficulty that the day succeeded in breaking.
```
Let's commit this sentence. If you use `git commit` as before, you will notice that this change has not yet been "staged". We can either run `git add moby.txt` to stage it (useful for commits with many files to be added), or just commit the file directly:

```
$ git commit moby.txt -m 'Added another sentence of Moby Dick from memory'
```

What does our commit history look like now?
```
$ git log
commit 3efacaf57c09387c701110d73f354286e3d4e669 (HEAD -> master)
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:13:15 2019 +0000

    Added another sentence of Moby Dick from memory

commit 81ec9e18c39dc6919146fcb9677a11a31d76719d
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:12:40 2019 +0000

    Added first sentences of Moby Dick
```

You will notice that `git log` provides us with a long commit hash, who made the commit and when, and tells us where the current `HEAD` is.

Those of you who have ever read a book might realise that my memory of Moby Dick isn't very good, so let's fix that. Either edit `moby.txt` in your editor, or use the following `sed` command to fix the first sentence.

```
$ sed -i 's/Moby/Ishmael/g' moby.txt
$ git diff
diff --git a/moby.txt b/moby.txt
index 6a234d6..e966d0d 100644
--- a/moby.txt
+++ b/moby.txt
@@ -1,2 +1,2 @@
-Call me Moby. Some years ago - never mind how long precisely - having little or no money in my purse, and nothing particular to interest me on shore, I thought I would sail about a little and see the water part of the world.
+Call me Ishmael. Some years ago - never mind how long precisely - having little or no money in my purse, and nothing particular to interest me on shore, I thought I would sail about a little and see the water part of the world.
 The morning was so damp and misty that it was only with great difficulty that the day succeeded in breaking.
```

You will now see that our `diff` compares the line as it exists in `HEAD`, and after our changes. You should now `commit` this change before we try some other sub-commands.

If you're paying attention to the important stuff in this tutorial (the literature), you will notice that the last sentence we added was actually from Dostoyevsky's "The Idiot", which is the book I wanted to type out for some reason(?) all along. Easy mistake to make, I know. Let's fix that! 

First, let's rename our file to something more appropriate, using `git mv` to ensure that the history of the file is preserved across the rename (using just Linux's `mv` would make Git think we had deleted the file and made a completely new unrelated file). We will then use a magic `sed` one-liner to replace the first line in our file with the correct sentence from The Idiot, then commit the file in anger at how we could have been so stupid.

```
$ git mv moby.txt idiot.txt
$ sed '1s/.*/Towards the end of November, during a thaw, at nine o' clock one morning, a train on the Warsaw and Petersburg railway was approaching the latter city at full speed.' idiot.txt
$ git commit idiot.txt -m 'Fixed mistake where I wrote out Moby Dick instead of The Idiot, like The Idiot I am'
``` 

Coming back 5 minutes later, you think that you probably shouldn't have written that commit message, so let's fix that by writing something more suitable, that removes the blame from us.
```
$ git commit --amend
```

Just to prove our repository has kept all of its history, and get the `diff` from our first commit:
```
$ git log
commit a0e8df557279270a9fa686c0ef7a44e347c189ce (HEAD -> master)
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:40:41 2019 +0000

    Fixed subtle bug in Linux kernel that caused Moby Dick to be written to file instead of The Idiot

commit e2b7ad72976d4c00ab6d0214ab41d3c13869c2dd
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:32:41 2019 +0000

    Fixed name in first sentence of Moby Dick

commit 92b27f011520c0ae99261203bb12b4fa4b11bbf4
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:11:58 2019 +0000

    Added another sentence of Moby Dick from memory

commit 473e3ad8df6fd2b6602a71bb1d764d7e4898b1ef
Author: Matthew Evans <me388@cam.ac.uk>
Date:   Mon Jan 28 21:11:23 2019 +0000

    Added first sentences of Moby Dick

$ git diff 473e3a
diff --git a/idiot.txt b/idiot.txt
new file mode 100644
index 0000000..9925ab9
--- /dev/null
+++ b/idiot.txt
@@ -0,0 +1,2 @@
+Towards the end of November, during a thaw, at nine o clock one morning, a train on the Warsaw and Petersburg railway was approaching the latter city at full speed.
+The morning was so damp and misty that it was only with great difficulty that the day succeeded in breaking.
diff --git a/moby.txt b/moby.txt
deleted file mode 100644
index c256b41..0000000
--- a/moby.txt
+++ /dev/null
@@ -1 +0,0 @@
-Call me Moby. Some years ago - never mind how long precisely - having little or no money in my purse, and nothing particular to interest me on shore, I thought I would sail about a little and see the water part of the world.
```
(Note how we only needed to use the first 6 characters of the commit hash when running `git`. Alternatively we could have used `git diff HEAD~4` to reference the commit 4 behind the current `HEAD`.

#### Remote version control
#### `mv`

Does the same as Linux's `mv`, but keeps tracking the file in Git. Otherwise, Git treats the old file as deleted, and the new filename as a brand new file with no history.

> `git mv old_name.txt new_name.txt`

#### `rm`

Remove a file from disk, and from any further Git tracking. The history of the file will remain under version control, as well as all previous versions.

> `git rm old_file.txt`
