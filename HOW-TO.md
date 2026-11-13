# HOW-TO

This how-to uses Git as an example, but the same steps should apply in all VCS.
It looks huge, but try to understand what is happening.

## naming vendor tags

When working with vendor branches, you will need to choose a tag to define the vendor versions.

1. If the vendor provides a clean tag such as `v1.0`, start off it
1. Sometimes the vendor never creates tags. In that case, start with a variant of an ISO date of today's date (or a vendor date if it is provided somewhere), e.g YYYY-MM-DD_HH-MM
1. to avoid tag name clashes, choose a format that organises tags and avoids clashes, such as `vendor/[<vendor name>/]<vendor project name>/<vendor tag>`.

## naming a vendor branch

Recommended branch naming: `vendor/[<vendor name>/]<vendor project>`

The "`vendor/`" prefixes are meant to draw the attention to the use of vendor branching and will also help in organise and deconflict refnames.

A similar approach *could* be used for the vendor directory, but is not essential.

## 1/ define a **new vendor**:

1. **create a vendor branch** to import the vendor.
```
git switch --orphan=<vendor branch> # your project directory will become empty
git commit --allow-empty -m "create vendor XXX"
```

The branch is initially unrelated to your own repository, therefore make it an orphan.
You need an empty commit.

### If the vendor publishes via a Git repo

You are lucky. Use `git subtree add` to create the vendor directory and populate it:

```
# optional; a Git trick to go to to GITROOT. Needed?
cd `git rev-parse --show-toplevel` 

git subtree add --prefix=<path to vendor dir> <vendor URL> <vendor Git ref>
...
```

Note about `git subtree --squash`:

- avoid it when you intend to contribute back to the vendor. *We all want that, right?* 
- use it to reduce disk space

### If the vendor uses another VCS

Manually create the vendor directory to hold all vendor artefacts. Even for *one file* your should use a directory.

1. decide on and create a suitable **vendor directory** tree (e.g. `vendor/CELESTRAK/SGP4`) that will hold the vendor artefacts. The vendor directory can also reside inside a normal directory.
1. **explode** (unzip/copy/import/...) the external data into the (until now empty) vendor directory. This is called a *vendor drop*.
1. Make sure that extracted file/directory *names* do not contain version information as a kind of implicit versioning scheme. Rename when needed. (Example: ``geonames.2.3.tgz`` might untar into ``geonames-2.3/ ``)

### Finalize the vendor branch creation

1. **Commit** the vendor branch. Make sure to add at least some information on the vendor version in the commit message
```
git add -A && git commit -m "import vendor XXX version YYY"
```

1. **Tag** the vendor branch, e.g. `vendor/geonames/1.0`.

```
git tag <vendor branch> <vendor tag>
```

### Merge locally and optionally tweak

1. **Switch** to your task branch
1. **merge** the vendor branch; avoid fast forward for clarity.
```
git merge --allow-unrelated-histories --no-ff <vendor branch>
```
1. Add and commit a ``VENDOR.md`` file to ``vendor/CELESTRAK/`` that contains relevant **metadata**
	- consider including the keyword "VENDOR" somewhere so that it can also be grepped if needed.
	- add instructions to help finding back the source. 
	- Make sure to include any PDF or comparable document that documents the vendor artefact but was not included by the vendor, as well as where that PDF, data file, ... can be found.
	- provide an example CLI command (**especially when using `git subtree`!**) that can easily be pasted in a terminal to update the vendor. Maybe a script can help?
1. Do whatever is needed to **localise** the vendor artefacts (source code, data, ...) into your project. This is done in your task branch, never in the vendor branch. Often, source code will not run or compile completely. Sometimes, data needs to be transformed; include scripts to run migrations/conversions; future vendor drops will require them.

*Do not delete the vendor branch.*

### Integrate

1. Have your task branch merged into your main branch. Your project now contains folowing:
	- a vendor branch and a vendor tag
	- a new vendor directory
	- optionally, vendor code that has been modified in `master` to make the vendor work seamlessly in your project
1. **Remember to also push vendor branch and tag to your central repo.**

## 2/ the vendor publishes a new release

1: perform a **vendor drop**:

```
git switch <vendor branch>
```

### ... you used `git subtree`?

The VENDOR file should clarify that.

You are lucky.

Use `git subtree pull`

```
cd `git rev-parse --show-toplevel` # is this needed?
git subtree pull --prefix=<path to vendor dir> <vendor URL> <vendor Git ref>
```

**Using the same prefix path is essential.** The `subtree` arguments are not "remembered" (such as a pull from a remote), so the vendor path and URL are needed (for now).  That's why you need to add an example `git subtree` command in the VENDOR file.
*Otherwise you will end up with many copies of your vendor artefacts scattered over many directories...*

### ... otherwise

**empty** the vendor directory  

```
cd <vendor directory>
git ls-files -z | xargs -0 rm -f # remove everything. or simply `rm`?
```
(see ``git help git-rm`` for details, search for "[vendor](https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a)". *Do notice BTW that the concept is mentioned in Git*)

2: replace/**explode**/unzip/untar/...  

3: **Commit** the vendor drop  

```
   git add -A
   git commit
```

### Finally, round up the vendor drop

4: **Tag** the vendor branch with a vendor tag

5: Merge the updated vendor branch to ``master`` (or, more probably, via an intermediate task/feature/bugfix branch, often in order to update local stuff). Run migration scripts if needed.

Deal with conflicts when needed. Such conflicts are expected to arise when the vendor changed something that was also changed locally, or when your project contributed to the vendor.

This is also needed when the external data disappears: in that case, mention that the external data is no longer available to the public to avoid developers searching for it (or even worse, continue with a copy that still exists elsewhere!! Such a copy does not belong on the vendor branch).

## 3/ want to contribute to the vendor project?

It may happen that you find a bug in the vendor artefacts and can provide a fix.
Here are the steps to follow.
Remember: *local modifications* are not meant to be shared with the vendor.

1. create a "contribution" branch **off a vendor tag**. Always start off an official version that is known by the vendor.
1. apply changes as necessary. merge and test locally. (this is the most git-intensive part). do not change the vendor branch itself.
1. send the contribution branch to the vendor as a pull request or patch (see Notes on patching)
1. merge the contribution branch to your local branches (task branch, main, ...)

## 4/ you never modified the vendor code, and now you want to...

This is the easy one: create the vendor branch. Merge back to master. And then start modifying.
It is assumed that the vendor artefacts already live in a separate directory, otherwise you first need to move those into a directory.

## 5/ converting existing copy-pasted artefacts to their original vendor status

This is not discussed for now. You should have followed the vendor branch strategy ;-)
This is hard and requires courage; most will simply continue to dabble in their local copy.

The steps to follow *likely* include

1. create the vendor
1. rip out the old copy-pasta
1. merge the vendor branch
1. the hard part: re-apply all changes that were performed on the original copy-pasta

