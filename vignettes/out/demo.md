<!-- Generated by galley: do not edit by hand -->

## Introduction

This guide will demonstrate how to use {fledge}, using a mock R package
as an example. We are going to call our package “{SuperFrob}”. We will
develop it from scratch and also maintain a changelog as the development
progresses. Finally, we will demonstrate how this changelog can
eventually be converted to release notes when the package is submitted
to CRAN.

This guide shows mostly R code, and uses Bash shell code for Git
commands.

## Set up the development environment

Before we start development for {SuperFrob}, we set up the basic
development environment required for any typical R package.

### Create a package

We will start by creating a new package. For this demo, the package is
created in a temporary directory. A real project will live somewhere in
your home directory.

``` r
tempdir <- file.path(tempdir(), "fledge")
dir.create(tempdir)
```

The `usethis::create_package()` function sets up a package project ready
for development. The output shows the details of the package created.

``` r
pkg <- usethis::create_package(file.path(tempdir, "SuperFrob"))
```

    ## [32m✔[39m Creating [34m'/tmp/RtmpGalley/fledge/SuperFrob/'[39m
    ## [32m✔[39m Setting active project to [34m'/tmp/RtmpGalley/fledge/SuperFrob'[39m
    ## [32m✔[39m Creating [34m'R/'[39m
    ## [32m✔[39m Writing [34m'DESCRIPTION'[39m
    ## [34mPackage[39m: SuperFrob
    ## [34mTitle[39m: What the Package Does (One Line, Title Case)
    ## [34mVersion[39m: 0.0.0.9000
    ## [34mDate[39m: 2020-12-12
    ## [34mAuthors@R[39m (parsed):
    ##     * Kirill Müller <krlmlr+r@mailbox.org> [aut, cre] (<https://orcid.org/0000-0002-1416-3412>)
    ## [34mDescription[39m: What the package does (one paragraph).
    ## [34mLicense[39m: GPL-3
    ## [34mURL[39m: https://github.com/krlmlr/fledge,
    ##     https://krlmlr.github.io/fledge
    ## [34mBugReports[39m: https://github.com/krlmlr/fledge/issues
    ## [34mEncoding[39m: UTF-8
    ## [34mLazyData[39m: true
    ## [34mRoxygen[39m: list(markdown = TRUE)
    ## [34mRoxygenNote[39m: 7.1.1.9001
    ## [32m✔[39m Writing [34m'NAMESPACE'[39m
    ## [32m✔[39m Setting active project to [34m'/home/kirill/git/R/fledge'[39m

In an interactive RStudio session, a new window opens. Users of other
environments would change the working directory manually. For this demo,
we manually set the active project.

``` r
usethis::proj_set()
## [32m✔[39m Setting active project to [34m'/tmp/RtmpGalley/fledge/SuperFrob'[39m
```

The infrastructure files and directories that comprise a minimal R
package are created:

``` r
fs::dir_tree()
## [01;34m.[0m
## ├── DESCRIPTION
## ├── NAMESPACE
## └── [01;34mR[0m
```

### Create and configure a Git repository

Next we set up this package for development and create a Git repository
for the package. We use `git init` to get a predictable branch name.

``` bash
git init --initial-branch=main
## Initialized empty Git repository in /tmp/RtmpGalley/fledge/SuperFrob/.git/
```

In interactive mode, the `usethis::use_git()` function creates an
initial commit, and the repository is in a clean state. In the demo this
needs to be carried out manually.

``` bash
git add .
git commit -m "Initial commit."
## [main (root-commit) 6b7a327] Initial commit.
##  2 files changed, 21 insertions(+)
##  create mode 100644 DESCRIPTION
##  create mode 100644 NAMESPACE
```

``` bash
git log --numstat
git status
## commit 6b7a327e5f9aab2cbffb96e564dadba10b4fe980
## Author: Kirill Müller <krlmlr+r@mailbox.org>
## Date:   Sat Dec 12 12:47:37 2020 +0000
## 
##     Initial commit.
## 
## 19   0   DESCRIPTION
## 2    0   NAMESPACE
## On branch main
## nothing to commit, working tree clean
```

For working in branches, it is recommended to turn off fast-forward
merging:

``` bash
git config merge.ff false
```

An alternative is to use squash commits.

### Create initial NEWS.md file

An initial NEWS file can be created with `usethis::use_news_md()`.

``` r
usethis::use_news_md()
## [32m✔[39m Writing [34m'NEWS.md'[39m
```

Let’s take a look at the contents:

``` r
news <- readLines(usethis::proj_path("NEWS.md"))
cat(news, sep = "\n")
## # SuperFrob 0.0.0.9000
## 
## * Added a `NEWS.md` file to track changes to the package.
```

This file needs to be tracked by Git:

``` bash
git add .
git status
git commit -m "Initial NEWS.md ."
## On branch main
## Changes to be committed:
##   (use "git restore --staged <file>..." to unstage)
##  new file:   NEWS.md
## 
## [main 0e11567] Initial NEWS.md .
##  1 file changed, 3 insertions(+)
##  create mode 100644 NEWS.md
```

## The development phase

### Create an R file

Now we start coding in the functionality for the package. We start by
creating the new R file called `super.R` and adding frobnication code.

``` r
usethis::use_r("super")
## [31m●[39m Edit [34m'R/super.R'[39m
## [31m●[39m Call [90m`use_test()`[39m to create a matching test file
writeLines("# frobnicate", "R/super.R")
```

We commit this file to Git with a relevant message. Note the use of the
bullet (-) before the commit message. This indicates that the message
should be included in `NEWS.md` when it is updated.

``` bash
git add .
git commit -m "- Add support for frobmication."
## [main ea0a1ab] - Add support for frobmication.
##  1 file changed, 1 insertion(+)
##  create mode 100644 R/super.R
```

### Create a test

The code in `super.R` warrants a test:

``` r
usethis::use_test("super")
## [32m✔[39m Adding [34m'testthat'[39m to [32mSuggests[39m field in DESCRIPTION
## [32m✔[39m Setting [32mConfig/testthat/edition[39m field in DESCRIPTION to [34m'3'[39m
## [32m✔[39m Creating [34m'tests/testthat/'[39m
## [32m✔[39m Writing [34m'tests/testthat.R'[39m
## [32m✔[39m Writing [34m'tests/testthat/test-super.R'[39m
## [31m●[39m Edit [34m'tests/testthat/test-super.R'[39m
cat(readLines("tests/testthat/test-super.R"), sep = "\n")
## test_that("multiplication works", {
##   expect_equal(2 * 2, 4)
## })
```

In a real project we would substitute the testing code from the template
by real tests. In this demo we commit straight away, again with a
bulleted message.

``` bash
git add .
git status
git commit -m "- Add tests for frobnication."
## On branch main
## Changes to be committed:
##   (use "git restore --staged <file>..." to unstage)
##  modified:   DESCRIPTION
##  new file:   tests/testthat.R
##  new file:   tests/testthat/test-super.R
## 
## [main cd7fc85] - Add tests for frobnication.
##  3 files changed, 10 insertions(+)
##  create mode 100644 tests/testthat.R
##  create mode 100644 tests/testthat/test-super.R
```

### Update NEWS.md

We use `fledge::bump_version()` to assign a new dev version number to
the package and also update `NEWS.md`.

``` r
fledge::bump_version()
## → Scraping [32m[32m4[32m[39m commit messages.
## [32m✔[39m Found [32m[32m2[32m[39m NEWS-worthy entries.
## 
## ── [1m[1mUpdating NEWS[1m[22m ──
## 
## → Adding new entries to [34m[34mNEWS.md[34m[39m.
## 
## ── [1m[1mUpdate Version[1m[22m ──
## 
## [32m✔[39m Package version bumped to [32m[32m0.0.0.9001[32m[39m.
## → Adding header to [34m[34mNEWS.md[34m[39m.
## → Committing changes.
## 
## ── [1m[1mTagging Version[1m[22m ──
## 
## → Creating tag [32m[32mv0.0.0.9001[32m[39m with tag message derived from [34m[34mNEWS.md[34m[39m.
## [31m●[39m Edit [34m'NEWS.md'[39m
## [33m![39m Call [30m[47m[30m[47m`fledge::finalize_version()`[47m[30m[49m[39m.
## NULL
```

### Review NEWS.md

Let us see what `NEWS.md` looks like after that bump.

``` r
news <- readLines("NEWS.md")
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## # SuperFrob 0.0.0.9001
## 
## - Add tests for frobnication.
## - Add support for frobmication.
## 
## 
## # SuperFrob 0.0.0.9000
## 
## * Added a `NEWS.md` file to track changes to the package.
```

While reviewing we notice that there was a typo in one of the comments.
Let’s fix the typo:

``` r
news <- gsub("frobmication", "frobnication", news)
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## # SuperFrob 0.0.0.9001
## 
## - Add tests for frobnication.
## - Add support for frobnication.
## 
## 
## # SuperFrob 0.0.0.9000
## 
## * Added a `NEWS.md` file to track changes to the package.
writeLines(news, "NEWS.md")
```

This does not affect the original commit message, only `NEWS.md`.

### Finalize version

After tweaking `NEWS.md`, it is important to use
`fledge::finalize_version()` and not to commit manually. This ensures
that the tag is set to the correct version in spite of the `NEWS.md`
update. It should be called when `NEWS.md` is manually updated.

``` r
fledge::finalize_version()
## → Resetting to previous commit.
## → Committing changes.
## 
## ── [1m[1mTagging Version[1m[22m ──
## 
## → Deleting tag [32m[32mv0.0.0.9001[32m[39m.
## → Creating tag [32m[32mv0.0.0.9001[32m[39m with tag message derived from [34m[34mNEWS.md[34m[39m.
```

When done, we push to the *remote* (GitHub or another Git service) with
tags using `fledge::finalize_version(push = TRUE)`, as advised. In this
demo, no remote repository is set up, and the push process is not shown.

### Change code and commit

{SuperFrob} with frobnication is tested, now we want to enhance with
super-frob. This requires changes to the code, and perhaps a new test.
We create a branch and switch to this branch to implement this.

``` bash
git checkout -b f-super-frob
## Switched to a new branch 'f-super-frob'
```

On the branch, separate commits are used for the tests and the
implementation. These commit messages do not need to be formatted
specially, because {fledge} will ignore them anyway.

``` r
usethis::use_test("frob")
## [32m✔[39m Writing [34m'tests/testthat/test-frob.R'[39m
## [31m●[39m Edit [34m'tests/testthat/test-frob.R'[39m
```

``` bash
git add .
git status
git commit -m "Add super-frob tests."
## On branch f-super-frob
## Changes to be committed:
##   (use "git restore --staged <file>..." to unstage)
##  new file:   tests/testthat/test-frob.R
## 
## [f-super-frob 2dd13f8] Add super-frob tests.
##  1 file changed, 3 insertions(+)
##  create mode 100644 tests/testthat/test-frob.R
```

``` r
usethis::use_r("frob")
## [31m●[39m Edit [34m'R/frob.R'[39m
## [31m●[39m Call [90m`use_test()`[39m to create a matching test file
writeLines("# super-frob", "R/frob.R")
```

``` bash
git add .
git status
git commit -m "Add super-frob implementation."
## On branch f-super-frob
## Changes to be committed:
##   (use "git restore --staged <file>..." to unstage)
##  new file:   R/frob.R
## 
## [f-super-frob deafe71] Add super-frob implementation.
##  1 file changed, 1 insertion(+)
##  create mode 100644 R/frob.R
```

This branch can be pushed to the remote as usual. Only when merging we
specify the message to be included in the changelog, again with a
bullet.[1]

``` bash
git checkout main
git merge f-super-frob --no-commit
git status
git commit -m "- Super-frobnication enabled."
## Switched to branch 'main'
## Automatic merge went well; stopped before committing as requested
## On branch main
## All conflicts fixed but you are still merging.
##   (use "git commit" to conclude merge)
## 
## Changes to be committed:
##  new file:   R/frob.R
##  new file:   tests/testthat/test-frob.R
## 
## [main cbb9de3] - Super-frobnication enabled.
```

The same strategy can be used when merging a pull/merge/… request on
GitHub, GitLab, …: use bullet points in the merge commit message to
indicate the items to include in `NEWS.md`.

Now that we have added super-frobnication support to our package, it is
time to bump the version again.

``` r
fledge::bump_version()
## → Scraping [32m[32m2[32m[39m commit messages.
## [32m✔[39m Found [32m[32m1[32m[39m NEWS-worthy entries.
## 
## ── [1m[1mUpdating NEWS[1m[22m ──
## 
## → Adding new entries to [34m[34mNEWS.md[34m[39m.
## 
## ── [1m[1mUpdate Version[1m[22m ──
## 
## [32m✔[39m Package version bumped to [32m[32m0.0.0.9002[32m[39m.
## → Adding header to [34m[34mNEWS.md[34m[39m.
## → Committing changes.
## 
## ── [1m[1mTagging Version[1m[22m ──
## 
## → Creating tag [32m[32mv0.0.0.9002[32m[39m with tag message derived from [34m[34mNEWS.md[34m[39m.
## [31m●[39m Edit [34m'NEWS.md'[39m
## [33m![39m Call [30m[47m[30m[47m`fledge::finalize_version()`[47m[30m[49m[39m.
## NULL
news <- readLines("NEWS.md")
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## # SuperFrob 0.0.0.9002
## 
## - Super-frobnication enabled.
## 
## 
## # SuperFrob 0.0.0.9001
## 
## - Add tests for frobnication.
## - Add support for frobnication.
## 
## 
## # SuperFrob 0.0.0.9000
## 
## * Added a `NEWS.md` file to track changes to the package.
```

## Prepare for release

After multiple cycles of development, review and testing, we decide that
our package is ready for release to CRAN. This is where {fledge}
simplifies the release process by making use of the all the commit
messages that we provided earlier.

### Bump version for release

We wish to release this package as a patch and so we use
`fledge::bump_version()` with the “patch” argument.

``` r
fledge::bump_version("patch")
## → Scraping [32m[32m1[32m[39m commit messages.
## [32m✔[39m Found [32m[32m1[32m[39m NEWS-worthy entries.
## 
## ── [1m[1mUpdating NEWS[1m[22m ──
## 
## → Adding new entries to [34m[34mNEWS.md[34m[39m.
## 
## ── [1m[1mUpdate Version[1m[22m ──
## 
## [32m✔[39m Package version bumped to [32m[32m0.0.1[32m[39m.
## → Adding header to [34m[34mNEWS.md[34m[39m.
## → Committing changes.
## [36mℹ[39m Preparing package for release (CRAN or otherwise).
## [31m●[39m Edit [34m'NEWS.md'[39m
## [33m![39m Convert the change log in [34m[34mNEWS.md[34m[39m to release notes.
## [33m![39m After CRAN release, call [30m[47m[30m[47m`fledge::tag_version()`[47m[30m[49m[39m and
## [30m[47m[30m[47m`fledge::bump_version()`[47m[30m[49m[39m to re-enter development mode
```

This updates the version of our package to 0.0.1.

### Generate release notes

We review the `NEWS.md` that were generated by {fledge}:

``` r
news <- readLines("NEWS.md")
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## # SuperFrob 0.0.1
## 
## - Same as previous version.
## 
## 
## # SuperFrob 0.0.0.9002
## 
## - Super-frobnication enabled.
## 
## 
## # SuperFrob 0.0.0.9001
## 
## - Add tests for frobnication.
## - Add support for frobnication.
## 
## 
## # SuperFrob 0.0.0.9000
## 
## * Added a `NEWS.md` file to track changes to the package.
```

Some of the intermediate commit messages are not relevant in the release
notes for this release. We need to edit `NEWS.md` to convert the
changelog to meaningful release notes.

``` r
length(news) <- 5
news[3:5] <- c(
  "Initial CRAN release.",
  "",
  "Basic functionality: Super-frobnication."
)
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## Initial CRAN release.
## 
## Basic functionality: Super-frobnication.
writeLines(news, "NEWS.md")
```

Unlike with development versions, we commit the changes to `NEWS.md`
manually:

``` bash
git add .
git status
git commit -m "Update NEWS."
## On branch main
## Changes to be committed:
##   (use "git restore --staged <file>..." to unstage)
##  modified:   NEWS.md
## 
## [main 1a55745] Update NEWS.
##  1 file changed, 2 insertions(+), 18 deletions(-)
```

The package is now ready to be released to CRAN. I prefer
`devtools::use_release_issue()` to create a checklist of things to do
before release, and `devtools::submit_cran()` to submit. The
`devtools::release()` function is a more verbose alternative.

## After release

Some time passed and our {SuperFrob} package was accepted on CRAN. At
this stage, {fledge} can help to tag the released version and create a
new version for development.

### Tag version

It is now the time to tag the released version using the
`fledge::tag_version()` function.

``` r
fledge::tag_version()
## 
## ── [1m[1mTagging Version[1m[22m ──
## 
## → Creating tag [32m[32mv0.0.1[32m[39m with tag message derived from [34m[34mNEWS.md[34m[39m.
```

It is advised to push to remote, with `git push --tags` from the command
line, or your favorite Git client.

### Create GitHub release

If your package is hosted on GitHub, `usethis::use_github_release()`
creates a draft GitHub release from the contents already in `NEWS.md`.
You need to submit the draft release from the GitHub release page.

### Restart development

We will now make the package ready for future development. The
`fledge::bump_version()` takes care of it.

``` r
fledge::bump_version()
## → Scraping [32m[32m1[32m[39m commit messages.
## [32m✔[39m Found [32m[32m1[32m[39m NEWS-worthy entries.
## 
## ── [1m[1mUpdating NEWS[1m[22m ──
## 
## → Adding new entries to [34m[34mNEWS.md[34m[39m.
## 
## ── [1m[1mUpdate Version[1m[22m ──
## 
## [32m✔[39m Package version bumped to [32m[32m0.0.1.9000[32m[39m.
## → Adding header to [34m[34mNEWS.md[34m[39m.
## → Committing changes.
## 
## ── [1m[1mTagging Version[1m[22m ──
## 
## → Creating tag [32m[32mv0.0.1.9000[32m[39m with tag message derived from [34m[34mNEWS.md[34m[39m.
## [31m●[39m Edit [34m'NEWS.md'[39m
## [33m![39m Call [30m[47m[30m[47m`fledge::finalize_version()`[47m[30m[49m[39m.
## NULL
news <- readLines("NEWS.md")
cat(news, sep = "\n")
## <!-- NEWS.md is maintained by https://cynkra.github.io/fledge, do not edit -->
## 
## # SuperFrob 0.0.1.9000 (2020-12-12)
## 
## - Same as previous version.
## 
## 
## Initial CRAN release.
## 
## Basic functionality: Super-frobnication.
```

Push to remote.

[1] Note that we really need a merge commit here; the default is to
fast-forward which doesn’t give us the opportunity to insert the message
intended for the changelog. Earlier, we set the `merge.ff` config option
to `"false"` to achieve this.