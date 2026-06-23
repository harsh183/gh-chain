# `gh chain`

Don't know when you get off the `gh stack` [waitlist](https://github.github.com/gh-stack/) but still want some easy UX around stacking? A few lines of bash is all you need: [gh-chain](/gh-chain)

## Install

Quite similar to other `gh` CLI [extensions](https://cli.github.com/manual/gh_extension_install):
```sh
$ gh extension install harsh183/gh-chain
```

## Commands

1. `$ gh chain` - Stack a new `gh pr create` without having to track the previous PR
  - Identifies the previous branch we branched off
  - passes that into `gh pr create -B`
2. `$ gh chain q "commit message"` or `$ gh chain quick "commit message"` - For when you just want to stack all uncommited changes as the next layer
  - `git checkout -b` a placeholder branch with a timestamp.
  - `git add -A`
  - `git commit -m` with whatever message you pass
  - `gh chain` with the above steps
  - Need a real branch name, more granular adding, or multiple commits? Use `gh chain` similar to example 3.

If you want to save some keystrokes, try out these aliases
```sh
$ gh alias set c 'chain'
$ gh alias set cq 'chain quick'
```
## Example

Let us dogfood this very script to show how it works

1. Starting off main with the first version of this script

```sh
gh-chain (main) $ gh chain q "flesh out the script v1"
Switched to a new branch 'quick/20260622221236'
[quick/20260622221236 8027a43] flesh out the script v1
 1 file changed, 33 insertions(+), 29 deletions(-)
Base: main
? Where should we push the 'quick/20260622221236' branch? harsh183/gh-chain

Creating pull request for quick/20260622221236 into main in harsh183/gh-chain

? Title (required) flesh out the script v1
? Body <Received>
? What's next? Submit
remote:
remote:
To github.com:harsh183/gh-chain.git
 * [new branch]      HEAD -> quick/20260622221236
branch 'quick/20260622221236' set up to track 'origin/quick/20260622221236'.
https://github.com/harsh183/gh-chain/pull/1
```

2. Now let me make a small change and stack
```sh
gh-chain (quick/20260622221236|✔) $ vim gh-chain
gh-chain (quick/20260622221236|✚1) $ gh chain q "make number larger"
Switched to a new branch 'quick/20260622221349'
[quick/20260622221349 70e83f6] make number larger
 1 file changed, 1 insertion(+), 1 deletion(-)
Base: quick/20260622221236
? Where should we push the 'quick/20260622221349' branch? harsh183/gh-chain

Creating pull request for quick/20260622221349 into quick/20260622221236 in harsh183/gh-chain

? Title (required) make number larger
? Body <Received>
? What's next? Submit
remote:
remote:
To github.com:harsh183/gh-chain.git
 * [new branch]      HEAD -> quick/20260622221349
branch 'quick/20260622221349' set up to track 'origin/quick/20260622221349'.
https://github.com/harsh183/gh-chain/pull/2
```

3. But I don't always want to be quick, sometimes i want to name my branch, have multiple commits and still have nice UX.
```sh
gh-chain (quick/20260622221349|✔) $ vim gh-chain
gh-chain (quick/20260622221349|✚1) $ git checkout -b hd/add_better_print
Switched to a new branch 'hd/add_better_print'
gh-chain (hd/add_better_print|✚1) $ git add .
gh-chain (hd/add_better_print|●1) $ git commit -m "3/x make a better print"
[hd/add_better_print fe4b60c] 3/x make a better print
 1 file changed, 1 insertion(+), 1 deletion(-)
gh-chain (hd/add_better_print|✔) $ gh chain
Stack base: quick/20260622221349
? Where should we push the 'hd/add_better_print' branch? harsh183/gh-chain

Creating pull request for hd/add_better_print into quick/20260622221349 in harsh183/gh-chain

? Title (required) 3/x make a better print
? Body <Received>
? What's next? Submit
remote:
remote:
To github.com:harsh183/gh-chain.git
 * [new branch]      HEAD -> hd/add_better_print
branch 'hd/add_better_print' set up to track 'origin/hd/add_better_print'.
https://github.com/harsh183/gh-chain/pull/3
```

4. Then I did the same for this README.md once I was done
```sh
$ vim README.md
gh-chain (hd/add_better_print|✔) $ git status
On branch hd/add_better_print
Your branch is up to date with 'origin/hd/add_better_print'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	README.md

nothing added to commit but untracked files present (use "git add" to track)
gh-chain (hd/add_better_print|✔) $ gh chain quick "4/x Add a ReadMe for this"
Switched to a new branch 'quick/20260622223014'
[quick/20260622223014 0e6212d] 4/x Add a ReadMe for this
 1 file changed, 90 insertions(+)
 create mode 100644 README.md
Stack base: hd/add_better_print
? Where should we push the 'quick/20260622223014' branch? harsh183/gh-chain

Creating pull request for quick/20260622223014 into hd/add_better_print in harsh183/gh-chain

? Title (required) 4/x Add a ReadMe for this
? Body <Received>
? What's next? Submit
remote:
remote:
To github.com:harsh183/gh-chain.git
 * [new branch]      HEAD -> quick/20260622223014
branch 'quick/20260622223014' set up to track 'origin/quick/20260622223014'.
https://github.com/harsh183/gh-chain/pull/4
```

5. Now they showed up in the Pull Requests Tab

<img width="400" height="350" alt="Screenshot 2026-06-22 at 10 30 47 PM" src="https://github.com/user-attachments/assets/77cfef1a-0ab6-4768-9872-71715be45cdd" />

6. I merged these in order using the GitHub UI with merge, squash, and rebase strategies and they all worked great. Make sure to manually or [auto-delete](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-the-automatic-deletion-of-branches) a branch once merged for the base to automatically change like

<img width="746" height="93" alt="Screenshot 2026-06-22 at 10 33 06 PM" src="https://github.com/user-attachments/assets/8c9e025e-7538-481c-9c26-02186acee170" />

## Is this Serious?

Not very, this doesn't really handle many edge cases or more complex behavior you might want in a large organization, but it more or less hits the simple use case of stacking PRs using `gh` CLI without much work for my use cases. I think my main point is that the core of a stacking workflow is already there with tools we already have, even if it's a clunky execution.

If you're actually interested in this kind of stacking flow, I highly suggest trying out software and flows that's more meant for this: 
* [Gerrit](https://www.gerritcodereview.com/) - Spun out of Google Android that uses standard `git` commands but your stacked change at `git push origin HEAD:refs/for/<branch_name>` 
* `jj` [Jujutsu](https://github.com/jj-vcs/jj) - Spun out of Google as well, and even has a `git` backend. Warning, this doesn't support [git lfs](https://github.com/jj-vcs/jj/issues/80), but I would have used this more otherwise since it captures how I think about changes.
* `sl` [Sapling](https://github.com/facebook/sapling) - Based off Facebook era Phabricator-style stacking that's now open source. Warning, don't use it if you use `sl` for [Steam Locomotive](https://github.com/mtoyoda/sl)
* `git format-patch` combined with `git send-email` - The original `git` workflow that's still used by Linux and many projects that shows how git has always been aligned with today's stacking workflow.
* `glab stack` [GitLab Stack](https://docs.gitlab.com/cli/stack/) - Gitlab's take on the same concept while still having the standard git semantics.
* [Graphite](https://graphite.com/) - New paid tool for Stack aware code review that works with GitHub and seems to have an interesting [stack aware merge queue](https://graphite.com/blog/the-first-stack-aware-merge-queue) 
* `hg` [Mercurial](https://www.mercurial-scm.org/) - Largely used by big tech companies maintaining various backends and extensions, has a lot of support of this kind of chained stacking model tthrough a few different ways.
