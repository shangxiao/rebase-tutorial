# rebase-tutorial
A small tutorial on interactive rebasing

1. Checkout the branch `rebase-me`
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
