As we hope to eventually get this code upstreamed, we will generally follow all policies from the [official v8 project](https://v8.dev/docs/contribute).

## Issues

In general, all changes should be based on an [issue](https://github.com/v8-riscv/v8/issues). If you would like to work on an existing issue, first check if anyone is already assigned and if so, discuss with that person before beginning work. If you would like to work on a new problem or enhancement, first create an issue for it and discuss with the community. The best way to do this will be to [join us in Slack](https://forms.office.com/Pages/ResponsePage.aspx?id=8o_uD7KjGECcdTodVZH-3OiciJKG_BJHrqMNgnsFFqtUNlRUNEQ5QUgxNk0wVEVaTjJBTDNOMDNIQS4u).

When creating a new issue, please be descriptive and include all relevant details, enough that someone completely unfamiliar with the problem or proposal can understand. Be sure to label your issue with the appropriate category (bug, enhancement, documentation, etc.). If you plan to work on the issue yourself, feel free to self-assign, otherwise, you can leave it unassigned for someone else to pick up.

If you plan to begin work on an issue, first verify that it is not already assigned to someone else. If it is already assigned, but you have some special reason to work on this issue, be sure to communicate with the assigned person. If it is unassigned, assign it to yourself before beginning work so that others know you are working on it. Use the issues comments to update your status regularly and communicate any important information to the community.

## Submitting your Changes

This project uses the standard GitHub mechanism for pull requests. See ["GitHub: Contributing to a Project"](https://git-scm.com/book/en/v2/GitHub-Contributing-to-a-Project) for the basic information on how to fork the repository, create a pull request, etc.

As mentioned above, we will follow the [guidelines](https://v8.dev/docs/contribute) for the upstream v8 project. The relevant part for submitting code is:

> The source code of V8 follows the Google C++ Style Guide so you should familiarize yourself with those guidelines. Before submitting code you must pass all our tests, and have to successfully run the presubmit checks:
> ```bash
> git cl presubmit
> ```
> The presubmit script uses a linter from Google, cpplint.py. It is part of depot_tools, and it must be in your PATH — so if you have depot_tools in your PATH, everything should just work.

Our CI job will run tests as well. These tests must all pass in order for your pull request to be considered. All pull requests must be reviewed and approved by at least one owner before being accepted. Please also review ["Using Git"] and ensure that your commit messages follow the guidelines laid out there.