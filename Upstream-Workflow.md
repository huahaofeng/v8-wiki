Now that our port is upstream, we need to modify our workflow to stay in sync with the upstream changes and streamline our work in this repository. The following is the initial proposal for this workflow. It is a work in progress and feedback, discussion, and changes are encouraged.

## Branches

- **master**: Automatically kept in sync with upstream master
- **riscv64**: Latest version of upstream that passes all RISC-V tests
  - If all tests pass on master, then sync riscv64 with master automatically
  - If any tests fail, do not merge master into riscv64; instead open issues for any failures
- **_other_**: Other development branches in progress

## Developer Workflow

As all changes will need to be merged upstream, all development branch ultimately need to be based on **master**. However, since there may occasionally be an active bug in **master**, such that the RISC-V tests do not pass, normal development branches should be based off of **riscv64** instead, in order to continue to make progress while someone else fixes the bug in **master**. In that case, the bug in **master** will need to be fixed and the development branch will have to be rebased before opening the upstream review. 

Once a changeset is ready, the developer should locally ensure that it passes all tests for our riscv64 target and if any target-independent code is touched, also verify the tests on x86/ARM. Since the upstream process has proven now to be efficient, we no longer plan to use pull requests in this repository, but to instead review the code directly upstream. For submitting our changes upstream, we follow V8's procedures defined in [Contributing to V8](https://v8.dev/docs/contribute). Once the reive is open, our team members can review it there, as well as request reviews from V8 developers as needed for shared code. A review requires a "LGTM" +1 from a committer responsible for each modified file in order to be merged. The submitter should request or notify reviewers as needed. The easiest way to do this is using the "FIND OWNERS" link at the top right of the review page. You may also need to request someone with the appropriate permissions to trigger the testing via the "Commit-Queue +1". Remember to manually add the `v8_linux64_riscv64_rel_ng` tests via the command line or the "CHOOSE TRY JOBS" link.

The simplified summary of this workflow is:

1. Create a development branch based on **master**
2. Make whatever changes in the development branch
3. Open an upstream review for the changeset
4. Request reviews from required reviewers
5. Pass CI tests (triggered with Commit-Queue +1)

Note that in this workflow, we never make changes directly to the **master** or **riscv64** branches. Changes to those only come from upstream. **riscv64** is always a subset of **master**: the last version working for RISC-V.

## Automation

To stay in sync with upstream, and to implement the workflow described above, we need to automate several steps.

- Automatically sync **master** with upstream
  - The tool should synchronize as often as possible, with just enough time to run our tests in between
- When the testing fails, automatically open a new issue, specifying the failure and the upstream commit that caused it
- If the tests pass, update the **riscv64** branch

The GitHub branch protection settings should be updated to allow only this tool to push to the **master** and **riscv64** branches to ensure that we do not accidentally make changes to those branches.
