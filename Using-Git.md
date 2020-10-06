[Git](https://git-scm.com/) is our preferred version control system for all projects. It provides all of the functionality we need for tracking changes, branching and merging, and distributed development. This document will cover the basics and often used commands/sequences. For details, please see [the](https://git-scm.com/doc) [many](https://try.github.io/) [excellent](https://en.wikipedia.org/wiki/Git) [resources](https://www.atlassian.com/git/tutorials) [on](https://www.tutorialspoint.com/git/index.htm) [the](https://git-scm.com/docs/gittutorial) [web](https://www.google.com/search?q=git+tutorial).

## Initial Setup

First, you will want to tell git your name and email. These will be used to identify you in your commit messages, and to match your commits to your GitLab user.

```
git config --global user.email "your.name@futurewei.com"
git config --global user.name "Your Name"
```

If you need to use a different name/email for a specific project, you can run these commands within that repository, omitting the `--global` flag, and it will be used only for that project.

## Cloning a Repository

Git is a distributed source code management tool, but for our projects, the master always lives on GitLab. When you begin workign on a project, you will first clone its repository from GitLab. From the project's front page, find the "Clone" button and click it to reveal the URLs for this project. Click the button to copy the SSH URL.

![gitlab screenshot](uploads/git1.png)

Paste this URL into the `git clone` command:

```
git clone ssh://git@ssh-gitlab.futurewei.com:30022/swlab/swe/v8.git
```

## Branches

Every project has a default branch and many other branches. The default branch is the master that all changes get merged into. For this project, the default branch is `riscv-porting-dev`. It should always be protected from directly committing to it. Instead, you need to make your changes on a new branch, then submit a merge request to have your branch merged into this master branch.

To switch to an existing branch, use:

```
git checkout branch-name
```

To create a new branch, you will first want to make sure you have the latest version of the default branch:

```
git checkout riscv-porting-dev
git pull
```

Now that you are at the correct starting point, create a new branch using:

```
git checkout -b my-new-branch
```

The name `my-new-branch` should be short, yet descriptive of the purpose of the branch. If it is related to an issue (which it usually should be), start the name with the issue number, for example, `123-floating-point-regs`.

## Making Changes

Now that you have created your branch, you can start making changes. Changes get committed into the repository, with an associated commit message. Try to keep each commit limited to a logical set of changes. When you are ready to commit a set of changes, there are two steps.

### Staging

First, we will "stage" the changes. I prefer to stage using the interactive command:

```
git add -p
```

This command shows you each chunk of changes, and gives you the option to either stage it (`y`) or skip it (`n`). This process allows you to review each change to ensure you did not leave unintended code (old commented out code, debug prints, etc.) in the source.

If you have made a large number of changes and are confident in the contents, you can also just use:

```
git add path/to/file
```

To stage all changes in a file or directory, without reviewing them.

### Committing

Once your changes are staged, you can now commit them.

```
git commit
```

This command will bring up an editor to write the commit message. To change the editor, set your `EDITOR` environment variable:

```
export EDITOR=vim
```

You may want to add the above export to your `~/.bashrc` file.

The commit message should follow a particular format to make it easy to read and understand when someone reviews it in the future. The article, ["How to Write a Git Commit Message"](https://chris.beams.io/posts/git-commit/) does a great job of explaining how and why to write a good message. The key points are:

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain what and why vs. how

Many editors will have plugins or settings to help you with the formatting of these messages.

If your commit is related to or closes an issue, be sure to include a line like this at the end of your message, and GitHub will automatically link it with the issue and/or close the issue:

```
See #23
Resolves #45
```

### Pushing

Once your changes are committed, they are in your repository. In order to share these changes with the team, and potentially merge them into the default branch, you will next want to push your branch to the server.

```
git push -u origin 98-fix-floating-point-regs
```

In the above command, `origin` is the name of the remote that you want to push to. In the simple case, you will only have one, and it will be named `origin`. If you have more than one, be sure to use the right name here.

`98-fix-floating-point-regs` is the branch name to use on the server. Usually, you will want this to match the name you used locally to keep it simple.

After you have pushed, you may make additional commits locally. Since the remote branch is already setup, subsequent pushes can just use:

```
git push
```

If you make a non-backwards compatible change to your local branch, for example by editing a commit that has already been pushed, you will need to force push to the server:

```
git push -f
```

Be careful doing this if anyone else has already pulled your branch from the server, as it will cause conflicts and be a real pain to cleanup.

When you push to GitLab, in the output, you will see a link to create a merge request for merging your branch into the default branch:

```
remote: To create a merge request for docs-new-merge-request, visit:
remote:   https://gitlab-instance.com/my-group/my-project/merge_requests/new?merge_request%5Bsource_branch%5D=my-new-branch
```

You can also open a merge request using the [web interface](https://gitlab.futurewei.com/swlab/swe/v8/merge_requests/new).

## Checking Current Status

At any time, you may want to see what the current status of your local repository is. At the highest level, you may want to use:

```
git status
```

The will show you what branch you are currently on, what files are staged, what files are changed, and what files are untracked.

```
$ git status
On branch 56-riscv-specific
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   src/base/platform/platform-posix.cc
        modified:   src/builtins/builtins-sharedarraybuffer-gen.cc
        modified:   src/codegen/interface-descriptors.cc
        modified:   src/codegen/riscv/interface-descriptors-riscv.cc

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   src/codegen/interface-descriptors.cc
        modified:   src/codegen/reloc-info.cc
        modified:   src/common/globals.h
        modified:   src/compiler/c-linkage.cc

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        out.txt
```

To see more detail, you may want to use:

```
git diff
```

This will show a diff of all unstaged changes in the branch.

To show the diff of staged changes, use:

```
git diff --staged
```

To show the difference between your current source and another branch, use:

```
git diff riscv-porting-dev
```

## Stashing Changes

Sometimes, you may want to temporarily hide un-committed changes in your repository to test something, switch to a different branch, pull new commits from the server, etc. Git provides the "stash" feature for this. Temporarily stash your changes with:

```
git stash
```

Then when you want to restore those changes, use:

```
git stash pop
```

The stash is a stack of changesets, so you can push and pop multiple levels of changes there. Be careful, because it is easy to lose track of what is in these stashes.

## Rebasing a Branch

If you have been working on a feature for a while, there is a good chance that the default branch has changed since you first created your branch. In order to merge your branch in, you'll need to rebase it. Rebasing is the process of moving the changes in your branch onto the top of another branch. The image from [this tutorial](https://gcapes.github.io/git-course/10-rebasing/) helps show what happens when rebasing.

![git rebase](https://gcapes.github.io/git-course/fig/git-rebase.svg)

To rebase your branch, take the following steps.

First, pull the latest version of the default branch:

```
git checkout riscv-porting-dev
git pull
```

Next, switch back to your branch:

```
git checkout your-branch
```

Now, initiate the rebase:

```
git rebase riscv-porting-dev
```

In the best case, there are no conflicts and the rebase is complete:

```
$ git rebase riscv-porting-dev
First, rewinding head to replay your work on top of it...
Applying: Cleanup MIPS comments in RISC-V code
```

However, if there are conflicts between the new changes in the default branch and your changes in your branch, you will have to manually resolve those conflicts.

```
$ git rebase riscv-porting-dev
First, rewinding head to replay your work on top of it...
Applying: Cleanup MIPS comments in RISC-V code
Applying: Cause a conflict
Using index info to reconstruct a base tree...
M       src/codegen/riscv/macro-assembler-riscv.cc
Falling back to patching base and 3-way merge...
Auto-merging src/codegen/riscv/macro-assembler-riscv.cc
CONFLICT (content): Merge conflict in src/codegen/riscv/macro-assembler-riscv.cc
error: Failed to merge in the changes.
Patch failed at 0002 Cause a conflict
Use 'git am --show-current-patch' to see the failed patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

In the above output, we can see that there is a conflict in `src/codegen/riscv/macro-assembler-riscv.cc`. If this happens, edit the conflicting file(s) and look for the markers indicating the conflict:

```
void TurboAssembler::MovFromFloatResult(const DoubleRegister dst) {
<<<<<<< HEAD
  Move(dst, fa0);  // Reg fa0 is FP return value.
=======
  Move(dst, fa0);  // Reg xx0 is o32 ABI FP return value.
>>>>>>> Cause a conflict
}
```

Fix up any portions of the code where these markers are found (your editor may provide assistance for this) and save the file, then stage the file and continue the rebase:

```
git add src/codegen/riscv/macro-assembler-riscv.cc
git rebase --continue
```

If your fix of the conflict ends up deleting all changes from a commit, then you may see this message:

```
$ git rebase --continue
Applying: Cause a conflict
No changes - did you forget to use 'git add'?
If there is nothing left to stage, chances are that something else
already introduced the same changes; you might want to skip this patch.

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

In that case, you can skip this commit using (as suggested in the output):

```
git rebase --skip
```

 Continue this process until the rebase finishes. While resolving conflicts, it is useful to use `git status` to see which files still need to be resolved:

 ```
 $ git status
interactive rebase in progress; onto 5b74fd6126
Last commands done (2 commands done):
   pick 6aae986e4e Cleanup MIPS comments in RISC-V code
   pick 0fb2b85615 Cause a conflict
No commands remaining.
You are currently rebasing branch '56-riscv-specific' on '5b74fd6126'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)

        both modified:   src/codegen/riscv/macro-assembler-riscv.cc

no changes added to commit (use "git add" and/or "git commit -a")
```

### Applying Changes on Top

Sometimes, instead of rebasing, it is easier to manually apply your commit on top of the latest changes. We can use the cherry-picking functionality to do that. First, you will want to create a backup of your branch, then delete the original:

```
git branch your-branch-backup
git branch -D your-branch
```

Now, recreate your branch as a clone of the latest default branch:

```
git checkout riscv-porting-dev
git pull
git checkout -b your-branch
```

Now, you want to get the identifiers of your old commits, and cherry-pick them onto the current branch. You can get the commit identifier using `git log`, it is the hex string next to the word "commit":

```
git log your-branch-backup
```

Now that you have the commit ids that you need, cherry pick them into this branch:

```
git cherry-pick 5b74fd6126aadc09ca43b3c92786ae404ea34a44
```

This may also cause a conflict, like the rebase process, but since the conflict is only between your change and the latest changes (and not some intermediate change) it is often easier to resolve.

## The Nuclear Option

Occasionally, you may mess up your local branch beyond recovery and just want to get back to what is on the server. The following command will do that, but be careful because your local changes will not be recoverable.

```
git reset -- hard gitlab/riscv-porting-dev
```

In the above command, `gitlab` is the name of the remote server and `riscv-porting-dev` is the branch that you want to recover. This will replace the contents of your current branch with the contents of that branch on the server.
