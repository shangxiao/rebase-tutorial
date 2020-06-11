# rebase-tutorial
A small tutorial on interactive rebasing

## Goals
1. Show how basic rebasing works by "replaying" commits from the nearest common ancestor
2. Show the difference between `git rebase <new-base>` and `git rebase --onto <new-base> <from> <to>`
3. Using interactive rebase to:
    1. fix commit message typos
    2. reorder commits
    3. delete unwanted commits; and
    4. squash "wip" or "fixed something from 2 commits ago" type commits
4. Demonstrate "auto squashing" and "auto stashing"

## The Tutorial

### Setup

```
root ~ # git clone https://github.com/shangxiao/rebase-tutorial.git
Cloning into 'rebase-tutorial'...
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 17 (delta 4), reused 14 (delta 3), pack-reused 0
Unpacking objects: 100% (17/17), 2.32 KiB | 158.00 KiB/s, done.
root ~ # cd rebase-tutorial/
root ~/rebase-tutorial (master)#
```

### Checkout the branch `rebase-me`

```
root ~/rebase-tutorial (master)# git checkout rebase-me
Branch 'rebase-me' set up to track remote branch 'rebase-me' from 'origin'.
Switched to a new branch 'rebase-me'
```

### Demonstrate basic rebasing

Rebasing is a way of moving a particular section of your commit graph onto a new "base". The basic command is `git rebase <new-base>`.  In this example we're going to move the branch `rebase-me` onto `random-branch`.  You can see that `random-branch` and `rebase-me` both have a common ancestor of the commit that `master` is currently pointing at:

```
root ~/rebase-tutorial (rebase-me)# git log --graph --all --oneline
* 848f42c (origin/random-branch) Random branch edit
| * cea6fa3 (HEAD -> rebase-me, origin/rebase-me) Fix Bar
| * 5794f69 Delete me!
| * c0bc6c6 Add Bazz <-- This message is incorrect :(
| * c75ef00 Add Foo and Bar
|/
* d272513 (origin/master, origin/HEAD, master) Add some instructions
* 7f592d2 Initial commit
```

Rebasing works by finding this nearest common ancestor and "replaying" the commits from that point onto the new base:

```
root ~/rebase-tutorial (rebase-me)# git rebase origin/random-branch
Successfully rebased and updated refs/heads/rebase-me.
root ~/rebase-tutorial (rebase-me)# git log --graph --all --oneline
* 3e80f76 (HEAD -> rebase-me) Fix Bar
* e7cdb58 Delete me!
* b8b436a Add Bazz <-- This message is incorrect :(
* 3a9c088 Add Foo and Bar
* 848f42c (origin/random-branch) Random branch edit
| * cea6fa3 (origin/rebase-me) Fix Bar
| * 5794f69 Delete me!
| * c0bc6c6 Add Bazz <-- This message is incorrect :(
| * c75ef00 Add Foo and Bar
|/
* d272513 (origin/master, origin/HEAD, master) Add some instructions
* 7f592d2 Initial commit
```

Because git replays all the commits from the nearest common ancestor, we cannot "undo" the rebase by using the simple format as it will also include the commit "Random branch edit". We need to use the `--onto` flag:

(Notice that `rebase-me` has now diverged from its remote tracking branch as the commit hashes are newly created ones.  Another form of "undoing" would be to reset the branch to its remote tracking branch.)

```
root ~/rebase-tutorial (rebase-me)# git rebase --onto master origin/random-branch rebase-me
Successfully rebased and updated refs/heads/rebase-me.
root ~/rebase-tutorial (rebase-me)# git log --graph --all --oneline
* 474ed16 (HEAD -> rebase-me) Fix Bar
* a6c01d2 Delete me!
* 8af7b5e Add Bazz <-- This message is incorrect :(
* 2c1d7aa Add Foo and Bar
| * 848f42c (origin/random-branch) Random branch edit
|/
| * cea6fa3 (origin/rebase-me) Fix Bar
| * 5794f69 Delete me!
| * c0bc6c6 Add Bazz <-- This message is incorrect :(
| * c75ef00 Add Foo and Bar
|/
* d272513 (origin/master, origin/HEAD, master) Add some instructions
* 7f592d2 Initial commit
```

### The second part of rebasing: Interactive rebasing to edit manipulate commits

Here we can see that the commit message "Add Bazz" is incorrect - the commit actually adds a class called `Buzz`:

```
root ~/rebase-tutorial (rebase-me)# git log -p
commit 474ed16f4158566d557550b037319f2e55b68a06 (HEAD -> rebase-me)
Author: David Sanders <>
Date:   Wed Jun 10 14:58:50 2020 +1000

    Fix Bar

diff --git a/foo.py b/foo.py
index 7ce7703..00c3aeb 100644
--- a/foo.py
+++ b/foo.py
@@ -5,4 +5,4 @@ class Foo:

 class Bar:
     def bar(self):
-        print("Beer")
+        print("Bar")

commit a6c01d256a7de61559a786a0bca46371fcb5fcfe
Author: David Sanders <>
Date:   Wed Jun 10 17:13:01 2020 +1000

    Delete me!

commit 8af7b5ec7a2479158897ca4fec10941e77e3d94d
Author: David Sanders <>
Date:   Wed Jun 10 14:58:11 2020 +1000

    Add Bazz <-- This message is incorrect :(

diff --git a/buzz.py b/buzz.py
new file mode 100644
index 0000000..c04d24d
--- /dev/null
+++ b/buzz.py
@@ -0,0 +1,3 @@
+class Buzz:
+    def buzz(self):
+        print("Buzz")

...
```

We can fix this commit message with an interactive rebase. This will give 


2. Demonstrate basic rebasing (non-interactive):
    1. Rebase `rebase-me` onto `random-branch`: `git rebase random-branch`
    2. Back to `master` again: `git rebase --onto master random-branch rebase-me`
3. Fix the typo in commit with message "Add Bazz"
4. Show simple reordering of commits
5. Remove the commit with message "Delete me!"
6. Observer warning, abort and remove the commit with a drop action
7. Squash the "fixup" commit "Fix Bar"
8. Demonstrate `git commit --fixup` pairing with `git rebase -i --autosquash`
9. Demonstrate autostashing `git commit -i --autostash`
10. Set git config to automatically autosquash: `git config rebase.autoSquash true` and `git config rebase.autoStash true`

Further reading:
* https://git-scm.com/docs/git-rebase#Documentation/git-rebase.txt-rebasemissingCommitsCheck
* https://thoughtbot.com/blog/autosquashing-git-commits
* https://www.praqma.com/stories/git-autostash/
