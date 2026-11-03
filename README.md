# Vendor branching : importing external artifacts without copy-pasta

# DRAFT #

## BUSY
## TODO

This document is an n-th attempt at explaining the vendor branching mechanism...

As it is obvious that the problem is widespread, it feels unlikely that many users will change their minds... So this whole discussion is mainly oriented to those who are still fresh in the discipline...

*In short: copy-pasting artefacts is a seemingly easy way to get things imported in a project. The problem is the maintainance effort that will follow. Do not assume the data is "written in stone" and will never change any more (be it inside your project or externally). Instead, every time you import anything from outside your project, be it code or data, ***do not bluntly copy-paste***; don't just create a "fork" of foreign artefacts unless there really is no other way. Following describes the recommended way to import such external artefacts. The procedure looks daunting, but that is only appearance.*

Sometimes this problem is then solved by manual labor: porting the external changes in Stellarium, a laborous approach prone to bugs. **Extremely instructive use cases are presented on [Wikipedia](https://en.wikipedia.org/wiki/Software_rot#Forked_online_forum_example)**.

Usually references of some form are added to the source code, e.g. ftp, snail mail, http, ... The problem is that these references might **disappear** at some point in the future. **This happens all the time with personal pages.** An example can be found in [this comment](https://github.com/Stellarium/stellarium/blob/dd006bc4095790dba6ceb7fe485284a6804a9fd4/plugins/Satellites/src/Satellites.cpp#L1929-L1931): **you will get a 404**: the pages are *gone*. *Those pages should somehow have been imported as vendor data.*

Sometimes this can be solved by using package managers that automate the importing and updating of external "stuff" (typically code) and - important - allow to fine tune which version is to be imported; Python's `pip -e` is a great example of this. But package managers do not allow to modify the imported code out of the box *and* benefit from external updates. Also, package managers are not always the easiest way to deal with vendors, sometimes due to a lack of experience of contributing C++ developers. **Vendor branching is far easier to work with** than working with package managers, as all the required knowledge resides in how to deal with branching.

# Issues in other repositories
The list is in fact huge...

- https://github.com/tudat-team/tudat-resources/issues/11
- the KIK case demonstrates what happens when one doesn't vendor: 
	- https://www.theregister.com/2016/03/23/npm_left_pad_chaos/
	- https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code
	- https://arstechnica.com/information-technology/2016/03/rage-quit-coder-unpublished-17-lines-of-javascript-and-broke-the-internet/
	- https://www.activestate.com/blog/pythons-atomicwrites-and-software-dependency-availability-issues/

## Intro

Artefacts can be source code, data files, PDF files, supporting documentation...

Artefacts that can be managed by package managers are not discussed here. An inconvenience of package managers might be that local modifications might be more difficult to manage. This is precisely what vendor branching has been designed for.

Some artefacts are not available as text but in binary formats (eg [Yale Bright Star Catalog](http://tdc-www.harvard.edu/catalogs/catalogsb.html)).

The concept actually dates from the venerable [CVS](https://www.gnu.org/software/trans-coord/manual/cvs/cvs.html) and [SVN](https://subversion.apache.org/blog/)

- How I discovered vendor branches: https://svnbook.red-bean.com/en/1.8/svn.advanced.vendorbr.html
- and I indeed remember CVS already mentioned it long time ago in the Cederqvist PDF: https://www.gnu.org/software/trans-coord/manual/cvs/html_node/Tracking-sources.html (although it didn't mention how to contribute back)

### The problem

Notice how it is not always obvious to trace back the original files. In the case of ephemeride routines, these reside in "ancient", core Stellarium files, it is extremely likely that they were copied (and *maybe* recent changes have been manually  and painfully incorporated in Stellarium). One aspect of the recommended vendor branch procedure details how to include such *meta information* so as to help find back the (original, root) source at all times.

The external (source) version of such artifacts will often continue to evolve, but these modifications obviously will not be magically reflected in the project repository, thus *sometimes, if not often* leading to artifacts slowly getting totally outdated (hence the term "[code rot](https://en.wikipedia.org/wiki/Software_rot)").

Importing external artifacts therefore requires a specific (and in this case a *simple* - believe me, it is not rocket science - as well as generic) approach: the **Vendor Branch** approach. The proposed Vendor Branch approach is KISS: *simple to apply to novice programmers*. By the way, the approach is valid in any VCS, such as SVN (where it seems to have emerged), Git, Mercurial, ClearCase, TFS, CVS (where it already exists), you name it.

Importing external artefacts in vendor branches will also provide insight in what changed in those artefacts, just by inspecting the "diff" between successive vendor versions. Normally such changes should also be announced by the vendor.

## The solution

1. Keep external information alive (and updated) on a **separate** branch, called a "**vendor branch**" . Every copied dataset/tool/sourcecode lives unmodified in its own vendor branch (and possibly in its own (sub)directory; a subdirectory will always be needed in order to separate vendor artefacts from your project's). A vendor branch tracks a *pristine* copy/mirror of the external data.
1. Ideally, the external information is stored in a separate directory (e.g. ``external/<vendor>/<toolname>``). If all the vendor publishes is a single file, then it will have to live in some subdirectory.
1. **vendor tags** describe which version of the vendor artifacts has been imported (e.g. ``vendor/geonames/2021-08-21``; do use the ISO date/time format; if possible, prefef the vendor's tag info.)
1. The vendor branch is merged to wherever it is needed (normally a feature branch, which will eventually be merged to ``master``)
1. A ``VENDOR`` file - which is added in our local version of the vendor data - explains where the external/original information can be found, so that it can be updated when necessary.
1. If needed (which is often the case), external information is modified *locally* (usually via a feature/master branch, but *never* in its vendor branch)
1. Ideally, issues in the vendor artifacts should be reported to the vendor, so that updates can then be imported back via a fresh *vendor drop*. Alternatively, a developer can solve issues locally, but *never* in the vendor branch. Alternatively, a fix branch can be created from the vendor branch and a patch transmited to the vendor as well as merged locally.

Sometimes, conversion routines need to be written or updated so that external data fits with the project. These routines do not belong in the vendor branch, but in the project itself. Remember that every vendor drop starts with an emptied vendor directory.

## SHORT HOW-TO

### 1/ define a **new vendor**

Follow these steps in order to import external artefacts for the first time.

1. **create a vendor branch** to import the vendor (e.g. ``vendor/CELESTRAK``). If the vendor provides multiple products, create the needed subbranches. (e.g. ``vendor/CELESTRAK/SGP4``). The "``vendor/``" segment is only meant to draw the attention to the concept.

If the external project is a Git repo, use `git subtree add`:

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
And skip the remainder dealing with vendor dropping.

Otherwise, manually create the vendor directory

1. create a suitable **vendor directory** tree (e.g. ``vendor/CELESTRAK``) that will hold the external vendor artefacts. The vendor directory can also reside inside a normal directory
1. unzip/copy/import/**explode**... the external data into the (until now empty) vendor directory. This is called a *vendor drop*.
1. Make sure that extracted file/directory *names* do not contain version information as a kind of implicit versioning scheme. Rename when needed. (Example: ``geonames.2.3.tgz`` might untar into ``geonames-2.3/ ``)
1. **Commit** the vendor branch. Make sure to add at least some information on the vendor version in the commit message

	``git add -A && git commit``

1. **Tag** the vendor branch, e.g. ``vendor/geonames/1.0``. A path structure allows to group tags in a more structured way. If the vendor does not provide a clear version number, use the UTC date/time of the drop, formatted as ISO: ``vendor/geonames/2021-09-09T1200`` or a date/time as stamped by the vendor.
1. **Switch** to your task branch
1. **merge** (avoid fast forward for clarity) the vendor branch. *Do not delete the vendor branch.*
`` git merge --no-ff <vendor branch>``
2. Add and commit a ``VENDOR.md`` file to ``vendor/CELESTRAK/`` that contains relevant **metadata** and instructions to help finding back the source. Avoid top-level (domain) adresses, try to make life easy for anyone wanting to update the data. Consider including the keyword "VENDOR" somewhere so that it can be grepped if needed. Make sure to include any PDF or comparable document that documents the vendor artefact, as well as where that PDF, data file, ... can be found.
1. Do whatever is needed to **localise** the vendor artefacts (source code, data, ...) into your project. This is done in your task branch, never in the vendor branch. Often, source code will not run or compile completely. Sometimes, data needs to be transformed; include scripts to run migrations/conversions, because future vendor drops might require them.
1. Have your task branch **integrated** into your main branch. Your project now contains folowing:
	- a new vendor directory
	- a vendor branch and a vendor tag
	- optionally, modified vendor code to make the vendor work seamlessly in your project
1. **Remember to also push the *vendor* branch and tag**

### 2/ perform a **vendor drop**

 When the vendor publishes an update, follow these steps to ingest those changes.

*FIXME: markdown list item numbering issues here, due to code blocks...*

1: **Switch** to the vendor branch

```
git switch <vendor branch>
```
If the vendor provides a Git repository, use `git subtree pull`

```
cd `git rev-parse --show-toplevel`
git subtree pull -P <path to vendor dir> <vendor URL>
```

Otherwise

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

5: **Tag** the vendor branch with a vendor tag (``vendor/<vendor name>/<vendor product>/<tag>``) where `<tag>` is either a tag available from the vendor, or otherwise the ISO date/time of the vendor drop or a vendor's timestamp.

6: Merge the updated vendor branch to ``master`` (or, more probably, via an intermediate task/feature/bugfix branch, often in order to update local stuff). Run migration scripts if needed.

7. Deal with conflicts when needed. Such conflicts are expected to arise when the vendor changed something that was also changed locally, or when your project contributed to the vendor.

This is also needed when the external data disappears: in that case, mention that the external data is no longer available to the public to avoid developers searching for it (or even worse, continue with a copy that still exists elsewhere!! Such a copy does not belong on the vendor branch).

### 3/ contribute to the vendor project

It may happen that you find a bug in the vendor artefacts and can provide a fix.
Here are the steps to follow.
*Local modifications* are not meant to be shared with the vendor.

TODO: add the `git subtree` case

1. create a "contribution" branch **off a vendor tag**. Always start off an official version that is known by the vendor.
1. apply changes as necessary. merge and test locally. (this is the most git-intensive part). do not change the vendor branch itself.
1. send the contribution branch to the vendor as a pull request or patch (see Notes on patching)
1. merge the contribution branch to your local branches (task branch, main, ...)

### 4/ converting existing copy-pasted artefacts to their original vendor status

This is not discussed for now. You should have followed the vendor branch strategy ;-)
This requires courage; most will simply continue to dabble in their local copy.

The steps to follow include

1. create the vendor
1. rip out the old copy-pasta
1. merge the vendor branch
1. re-apply all changes that were performed on the original copy-pasta. This is probably a daunting task.

(TODO)

## Notes

### patching

In some circumstances you want to also contribute to the vendor's project.

If the vendor uses a Git repo, create a pull request and skip the remainder of this section on patching.

Otherwise you need to send a patch.
This is because your vendor directory contains totally different SHA-1 commits, so Git wouldn't know how to create a patch that can be applied on the vendor's side.

There are a few ways to handle this situation.

#### create a patch file with `git diff`

If your changes don't contain binary files, run a diff and send that to the vendor.
Remember to generate a diff relative to the vendor directory.

```
git diff --relative=<vendor path>  <branch base>..<branch head>
```

It might be good to review the generated text file, you never know... For example, to detect if the patch did touch binary files.

The vendor will apply your patch (probably via a branch) using the classical `patch` program

```
patch -p1 < patchfile
```

#### how to deal with binaries

If your patch adds/changes binary files, then supply those separately.

TO CONFIRM: edit the patch file, remove the references to binaries(?), and mention this to the vendor.

#### creating a patch using `git archive`

The easiest way (also to avoid the binaries issue) is to send an entire archive *of the vendor directory* of your branch.
The vendor will then ingest that in a branch (actually they are doing a kind of vendor drop...) and run their diff/merge/etc.

```
git archive --format=tgz --output=<tarfile>.tgz <head>:<path to vendor directory> [path]
```

This creates a tgz file (run `git archive --list` for the formats) of the commit `<head>` relative to the vendor directory dirname.

Note that this even works for a single vendor file, should you have chosen not to use a directory for the vendor artefact.

### how to vendor drop a remote git repo

Sometimes, it might be desirable to convert a Git repo to an archive in order to perform the vendor drop.

### what about `git submodules` (complex, delicate)

These are partial solutions offered by Git but do not provide the flexibility of vendor branching.
To expand further.

### what if external data is not available as text files

Sometimes, artefacts are not available as ready to ingest source code. But as a PDF that contains (parts of) source code. Or a binary formatted file.

Such artefacts cannot magically be turned into source code.

The idea is to store the artefact (PDF, binary file, ...) in a separate folder in the vendor branch, then painfully (yes...) extract the code into another separate folder in the vendor branch. And/or ask the author to provide a git repo, a file, archive, ...

If there is no tarball to explode, do not empty the vendor directory, but manually update what changed. Often this can be accomplished via scripts. (??)

And then only merge to main/master/...

### dealing with coding style/conventions

When modifying vendor code, stick to the vendor coding style/conventions. Rewriting for readbility etc. will likely lead to massive conflicts at the next vendor drop.

### dealing with trailing whitespace

*TODO: add directives how to deal with external artefacts originating from other platform environments.
This is not directly related to vendor branching.*

Git tips (TO CONFIRM):

- check your current global/local/worktree whitespace handling
```
	git config --get-all --show-origin core.whitespace
	git config --global core.whitespace trailing-space,-space-before-tab,indent-with-non-tab,cr-at-eol
```
- enable your repo's pre-commit hook (it checks for various whitespace issues. See also ``git help hooks``

### ...and platform end of line issues

*TODO: replace with ``.gitattributes`` ?*

- check line ending handling
```
	git config --get-all --show-origin core.autocrlf # empty == 'false'
	git config --get-all --show-origin core.safecrlf # empty == 'false'
	git config --get-all --show-origin core.eol # empty = 'native'
```
- make sure...
- as a **Windows** developer, that CRLF is converted to LF when comitting:
```
	$ git config --global core.autocrlf true
```
- as a **Linux** dev, ensure that
```
	git config --global core.autocrlf input
```
 See also
- ``git help config``
- https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration
- https://adaptivepatchwork.com/2012/03/01/mind-the-end-of-your-line/
- https://docs.github.com/en/get-started/git-basics/configuring-git-to-handle-line-endings

### Schema
Sometimes a format is accompanied by a metadata section describing the format of the data. This metadata is an important artefact to commit to an vendor branch. *Changes to the schema will be readily identified as diffs in the vendor branch. That's why such files must be ingested too.*

### When conflicts are no longer manageable

In some cases, merging vendor updates may become too difficult if not impossible. Welcome to merge hell? Nevertheless, vendor drops will continue to provide the means to port changes, by allowing the developer to study at least what changed in the vendor branch, and apply those changes ... manually.

``git rerere`` might become helpful.

### Importing vendor code written in other languages

If the artifact is not usable without extensive rewriting (e.g. Fortran code in a C++ project), it might still make sense to vendor the original file and keep it as a neutral (uncompiled) text file that will serve as an excellent reference.

This also applies for cases where explanatory/example (pseudo) sourcecode is offered; this code is essential documentation that the vendor might update occasionally.

### How to deal with module files that are updated externally and still not vendored internally

All hope is not lost! The basic idea is to create a vendor branch with the new artifact, and do some manual "undo-redo" work in the task branch .

### Version information in vendor file/directory paths

Sometimes, developers add directories/files with path names containing version information. An example is the FAQ file of [this landscape](http://www.alienbasecamp.com/Stellarium/sun.htm).

This is a bad practice, usually done by developers that do not have access to a good VCS. (Note also the CR/LF issue.)

In such cases, the version information should be removed before committing the vendor drop; this will present the additional advantage that the history of such a file becomes available. This is a delicate step, as the "pristine copy" requirement is no longer fulfilled. If filename information is part of the correct functioning of the imported artifacts (e.g. it is also referenced in vendor scripts, in linked data, etc), then you are facing a difficult problem. In such a case (for now), it might be better to not rename the vendor file paths at all and accept that file history must be discovered by other means (e.g. a manual diff). *TODO find a better way here.*

## See also

### vendor branching concept

- https://stackoverflow.com/questions/tagged/vendor-branch?sort=votes
- https://stackoverflow.com/questions/769786/vendor-branches-in-git
- https://en.wikipedia.org/wiki/Branching_%28version_control%29#Motivations_for_branching
- https://blog.bigsmoke.us/2009/07/20/svn-vendor-branches
- https://portal.perforce.com/s/article/3374
- https://stackoverflow.com/questions/1710027/arguments-for-and-against-including-3rd-party-libraries-in-version-control/1710050

### Github
- https://github.com/stars/axd1967/lists/vendor-branching
- new topic: https://github.com/topics/vendor-branching
- [a FreeBSD article for committers](https://github.com/freebsd/freebsd-doc/blob/main/documentation/content/en/articles/committers-guide/_index.adoc)
- this topic probably contains many more examples: https://github.com/topics/vendor

### Copy-pasta
- https://en.wikipedia.org/wiki/Software_rot
- https://en.wikipedia.org/wiki/Copypasta#Technology
- "*[copy-paste is evil](https://stackoverflow.com/questions/2490884/why-is-copy-and-paste-of-code-dangerous)*"
- https://www.cisa.gov/resources-tools/resources/product-security-bad-practices: _"Cache copies of all open-source dependencies within the manufacturer’s own build systems and do not update products or customer systems directly from unverified public sources."_ (Dead link, track https://github.com/cisagov/bad-practices/discussions/101)

### Git
- https://github.com/git/git/blob/master/Documentation/technical/unit-tests.adoc
- how to correctly vendor drop: https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a
- `git subtree`:
	- https://apenwarr.ca/log/20090430-a-new-alternative-to-git-submodules-git-subtree
	- https://github.com/git/git/blob/master/contrib/subtree/git-subtree.adoc
	- https://david.rothlis.net/vendor-branch/
	- https://www.atlassian.com/git/tutorials/git-subtree
- `git submodule`:
	- https://github.com/git/git/blob/master/Documentation/user-manual.adoc#submodules

### to be evaluated:
- a [rebasing vendor branch workflow](https://talesfromthearmchair.net/a-rebasing-vendor-branch-workflow-for.html)
	- if you read this far, we need to grok this
- https://github.com/brettlangdon/git-vendor
	- recommended (FWIW) by https://medium.com/opsops/git-vendor-295db4bcec3a)
	- looks dead
- https://github.com/thejoshwolfe/git-vendor
	- a script to automate vendor branching:
	- makes a comparison with submodule, subtree, brettlangdon
- how to deal with huge repos, such as the migration of TLE to spacedatastandards OMM: their repo is too big for the average developer to ingest *as a vendor branch*.

Think vendor branching is difficult? Eat [this](https://www.refontelearning.com/blog/tle-to-omm-six-digit-catalog-migration)!

## Related:

- https://github.com/axd1967/SGP4-vendor-branching
- https://github.com/axd1967/stellarium/blob/contrib/docs/vendor-branching/VENDOR_BRANCHING.md

## Elsewhere

- https://github.com/cpm-cmake/CPM.cmake/discussions/720
