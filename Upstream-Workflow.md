Now that our port is upstream, we need to modify our workflow to stay in sync with the upstream changes and streamline our work in this repository. The following is the initial proposal for this workflow. It is a work in progress and feedback, discussion, and changes are encouraged.

## Branches

- **master**: Automatically kept in sync with upstream master
- **riscv64**: Latest version of upstream that passes all RISC-V tests
  - If all tests pass on master, then sync riscv64 with master automatically
  - If any tests fail, do not merge master into riscv64; instead open issues for any failures
- **_other_**: Other development branches in progress

## Developer Workflow

As all changes will need to be merged upstream, all development branch ultimately need to be based on **master**. However, since there may occasionally be an active bug in **master**, such that the RISC-V tests do not pass, normal development branches should be based off of **riscv64** instead, in order to continue to make progress while someone else fixes the bug in **master**. In that case, the bug in **master** will need to be fixed and the development branch will have to be rebased before opening the upstream review. 

Once a development branch is ready for review, the developer should open a pull request in this repository, with the **riscv64** branch as the target. Reviewers can review the code, and make comments and suggest changes in this pull request, however, the pull request should not be merged. Instead, once a pull request has been approved by another v8-riscv member and the CI jobs have passed, then the developer should open an upstream review for those changes. Remember that this upstream review can only occur once any upstream changes have been resolved, and **master** == **riscv64** and the development branch is rebased on top of the fix.

The simplified summary of this workflow is:

1. Create a development branch based on **riscv64**
2. Make whatever changes in the development branch
3. Open a pull request in the v8-riscv GitHub repo with **riscv64** as the target
4. Get an approval from one or more v8-riscv members
5. Pass CI tests
6. If **riscv64** != **master**, wait for bugs on **master** to be resolved, then rebase development branch
7. Open upstream review

The slightly modified workflow for when a developer is fixing a problem in **master** which causes an error in RISC-V:

1. Create a development branch based on **master**
2. Make whatever changes in the development branch
3. Open a pull request in the v8-riscv GitHub repo with **master** as the target
4. Get an approval from one or more v8-riscv members
5. Pass CI tests
6. Open upstream review

Note that in this workflow, we never make changes directly to the **master** or **riscv64** branches. Changes to those only come from upstream. **riscv64** is always a subset of **master**: the last version working for RISC-V.

## Automation

To stay in sync with upstream, and to implement the workflow described above, we need to automate several steps.

- Automatically sync **master** with upstream
  - The tool should synchronize as often as possible, with just enough time to run our tests in between
- When the testing fails, automatically open a new issue, specifying the failure and the upstream commit that caused it
- If the tests pass, update the **riscv64** branch

The GitHub branch protection settings should be updated to allow only this tool to push to the **master** and **riscv64** branches to ensure that we do not accidentally make changes to those branches.