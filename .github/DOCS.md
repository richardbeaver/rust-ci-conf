# GitHub config and workflows

In this folder there is configuration for codecoverage, dependabot, and ci
workflows that check the library more deeply than the default configurations.

This folder can be or was merged using a --allow-unrelated-histories merge
strategy from <https://github.com/jonhoo/rust-ci-conf/> which provides a
reasonably sensible base for writing your own ci on. By using this strategy the
history of the CI repo is included in your repo, and future updates to the CI
can be merged later.

To perform this merge run:

```shell
git remote add ci https://github.com/jonhoo/rust-ci-conf.git
git fetch ci
git merge --allow-unrelated-histories ci/main
```

An overview of the files in this project is available at:
<https://www.youtube.com/watch?v=xUH-4y92jPg&t=491s>, which contains some
rationale for decisions and runs through an example of solving minimal version
and OpenSSL issues.

# Checklist for Setup

All changes that need to be made when using this CI config template in a new
project:

- Remove `safety.yml` if no unsafe code
    - `git rm <file-name>`
- Remove `nostd.yml` if not using targets with either no standard library or
  allocator

### Dependabot - `dependabot.yml`

- If project is a library, leave as is
- If project contains binaries:
    - Set `schedule.interval` for cargo packages to `weekly`
    - Remove `ignore` step

### Codecov - `codecov.yml`

- If project has a `main` file, add it to files to ignore

### Test - `workflows/test.yml`

- To run tests with max versions of transitive dependencies, instead of minimum:
    - Replace `-Zminimal-versions` on lines 77, 81, and 82
      with `-Zdirect-minimal-versions`
- If project needs OpenSSL:
    - Uncomment lines 96-99 to install OpenSSL on Windows builds
- Setting up Codecov
    - Set up repo on connected codecov.io account
    - Add codecov token as a GitHub repository secret
    - Add the following to the `with` section at end of file (not required for
      public repos but avoids failure due to rate limiting (except for PRs
      opened from forks))
        - `token: ${{ secrets.CODECOV_TOKEN }}`

