# HOW-TO

### 1/ define a **new vendor**:

Follow these steps in order to import external artefacts for the first time.

1. **create a vendor branch** to import the vendor (e.g. ``vendor/CELESTRAK``). If the vendor provides multiple products, you might want to create the needed subbranches instead (e.g. ``vendor/CELESTRAK/SGP4``). The "``vendor/``" segment is only meant to draw the attention to the concept.

### If the vendor publishes via a Git repo

You are lucky.

Use `git subtree add`:

```
git checkout -c <vendor branch>
cd `git rev-parse --show-toplevel` # (a Git trick to go to to GITROOT)
git subtree add -P <path to vendor dir> <vendor URL>
git tag <vendor branch>
git switch <taskbranch>
git merge <vendor tag>
...
```

Note: avoid `--squash` otherwise you will not be able to contribute back to the vendor.

### If the vendor does not use Git

Manually create the vendor directory to hold all vendor aretefacts. Even for *one file* your need  directory.

1. create a suitable **vendor directory** tree (e.g. ``vendor/CELESTRAK``) that will hold the external vendor artefacts. The vendor directory can also reside inside a normal directory
1. unzip/copy/import/**explode**... the external data into the (until now empty) vendor directory. This is called a *vendor drop*.
1. Make sure that extracted file/directory *names* do not contain version information as a kind of implicit versioning scheme. Rename when needed. (Example: ``geonames.2.3.tgz`` might untar into ``geonames-2.3/ ``)
1. **Commit** the vendor branch. Make sure to add at least some information on the vendor version in the commit message

	``git add -A && git commit``

### And finalize the vendor branch creation

1. **Tag** the vendor branch, e.g. ``vendor/geonames/1.0``. A path structure allows to group tags in a more structured way. If the vendor does not provide a clear version number, use e.g. the UTC date/time of the drop, formatted as ISO: ``vendor/geonames/2021-09-09T1200`` or a date/time as stamped by the vendor.

### Merge locally and optionally tweak

1. **Switch** to your task branch
1. **merge** the vendor branch; avoid fast forward for clarity.
`` git merge --no-ff <vendor branch>``
2. Add and commit a ``VENDOR.md`` file to ``vendor/CELESTRAK/`` that contains relevant **metadata** and instructions to help finding back the source. Consider including the keyword "VENDOR" somewhere so that it can also be grepped if needed.  Make sure to include any PDF or comparable document that documents the vendor artefact but was not included by the vendor, as well as where that PDF, data file, ... can be found.
1. Do whatever is needed to **localise** the vendor artefacts (source code, data, ...) into your project. This is done in your task branch, never in the vendor branch. Often, source code will not run or compile completely. Sometimes, data needs to be transformed; include scripts to run migrations/conversions, because future vendor drops will require them.

*Do not delete the vendor branch.*

### Integrate

1. Have your task branch merged into your main branch. Your project now contains folowing:
	- a vendor branch and a vendor tag
	- a new vendor directory
	- optionally, modified vendor code to make the vendor work seamlessly in your project
1. **Remember to also push the *vendor* branch and tag**

## 2/ the vendor publishes a new release:

Perform a **vendor drop**.

 When the vendor publishes an update, follow these steps to ingest those changes.

1: **Switch** to the vendor branch

```
git switch <vendor branch>
```

### ... via Git?

You are lucky.

*FIXME: markdown list item numbering issues here, due to code blocks...*

Use `git subtree pull`

```
cd `git rev-parse --show-toplevel`
git subtree pull -P <path to vendor dir> <vendor URL>
```

### Otherwise

2: **empty** the vendor directory  

```
cd <vendor directory>
git ls-files -z | xargs -0 rm -f # remove everything. or simply `rm`?
```
(see ``git help git-rm`` for details, search for "[vendor](https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a)". *Do notice BTW that the concept is mentioned in Git!*)  

3: replace/**explode**/unzip/untar/...  

4: **Commit** the vendor drop  

```
   git add -A
   git commit
```

### Finally, round up the vendor drop

5: **Tag** the vendor branch with a vendor tag (``vendor/<vendor name>/<vendor product>/<tag>``) where `<tag>` is either a tag available from the vendor, or otherwise the ISO date/time of the vendor drop or a vendor's timestamp.

## Merge

6: Merge the updated vendor branch to ``master`` (or, more probably, via an intermediate task/feature/bugfix branch, often in order to update local stuff). Run migration scripts if needed.

7: Deal with conflicts when needed. Such conflicts are expected to arise when the vendor changed something that was also changed locally, or when your project contributed to the vendor.

This is also needed when the external data disappears: in that case, mention that the external data is no longer available to the public to avoid developers searching for it (or even worse, continue with a copy that still exists elsewhere!! Such a copy does not belong on the vendor branch).

## 3/ want to contribute to the vendor project?

It may happen that you find a bug in the vendor artefacts and can provide a fix.
Here are the steps to follow.
Remember: *local modifications* are not meant to be shared with the vendor.

1. create a "contribution" branch **off a vendor tag**. Always start off an official version that is known by the vendor.
1. apply changes as necessary. merge and test locally. (this is the most git-intensive part). do not change the vendor branch itself.
1. send the contribution branch to the vendor as a pull request or patch (see Notes on patching)

### Finally

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

---
