# Vendor branching : importing external artifacts without copy-pasta

---
Document revisions

2026-11-05: v1-rc.1

- rewrite; split off 
	- Stellarium related issues to (DRAFT) https://github.com/axd1967/stellarium/blob/contrib/docs/vendor-branching/VENDOR_BRANCHING.md
	- SGP4 to (DRAFT) https://github.com/axd1967/SGP4-vendor-branching

---

## Intro

You copied artefacts from a fancy place, tweaked it and now you discover that meanwhile the fancy place updated their code. What now?
Artefacts can be source code, data files, PDF files, supporting documentation...

This document is the zillionth attempt at explaining the vendor branching mechanism.

## The problem

*In short: copy-pasting artefacts is a seemingly easy way to get things imported in a project. The problem is the maintainance effort that will follow. Do not assume the data is "written in stone" and will never change any more (be it inside your project or externally). Instead, every time you import anything from outside your project, be it code or data, ***do not bluntly copy-paste***; don't just create a "fork" of foreign artefacts unless there really is no other way.*

Sometimes this problem is then solved by manual labor: porting the external changes in Stellarium, a laborous approach prone to bugs. **Extremely instructive use cases are presented on [Wikipedia](https://en.wikipedia.org/wiki/Software_rot#Forked_online_forum_example)**.

Usually references of some form are added to the source code, e.g. ftp, snail mail, http, ... The problem is that these references might **disappear** at some point in the future. **This happens all the time with personal pages.** An example can be found in [this comment](https://github.com/Stellarium/stellarium/blob/dd006bc4095790dba6ceb7fe485284a6804a9fd4/plugins/Satellites/src/Satellites.cpp#L1929-L1931): **you will get a 404**: the pages are *gone*. *Those pages should somehow have been imported as vendor data.*

The approach looks daunting, but that is only appearance.

The external (source) version of such artifacts will often continue to evolve, but these modifications obviously will not be magically reflected in the project repository, thus *sometimes, if not often* leading to artifacts slowly getting totally outdated (hence the term "[code rot](https://en.wikipedia.org/wiki/Software_rot)").

## Concrete issues elsewhere

- https://github.com/tudat-team/tudat-resources/issues/11
- the KIK case demonstrates what happens when one doesn't vendor: 
	- https://www.theregister.com/2016/03/23/npm_left_pad_chaos/
	- https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code
	- https://arstechnica.com/information-technology/2016/03/rage-quit-coder-unpublished-17-lines-of-javascript-and-broke-the-internet/
- or what happens when one trusts external vendors:
	- https://www.bleepingcomputer.com/news/security/dev-corrupts-npm-libs-colors-and-faker-breaking-thousands-of-apps

---

## **A** solution: **vendor branching**

Sometimes this can be solved by using package managers that automate the importing and updating of external "stuff" (typically code) and - important - allow to fine tune which version is to be imported; Python's `pip -e` is a great example of this. But package managers do not allow to modify the imported code out of the box *and* benefit from external updates. Also, package managers are not always the easiest way to deal with vendors, sometimes due to a lack of experience of contributing (C++) developers. (An advanced example is [Activestate](https://www.activestate.com/platform/supported-languages/python).)

Artefacts that can be managed by package managers are not discussed here. An inconvenience of package managers might be that local modifications might be more difficult to manage. This is precisely what vendor branching has been designed for.

And sometimes there will be no package manager provided with the artefact. Often, the artefacts will live in zip or PDF files or HTML pages...

As it is obvious that the problem is widespread, it feels unlikely that many users will change their minds... So this whole discussion is mainly oriented to those who are still fresh in the discipline... And especially towards devs that are about to copy-paste.

**Vendor branching is far easier to work with** than working with package managers, as all the required knowledge resides in how to work with branching (and merging).

The concept already exists:

- in the venerable [CVS](https://www.gnu.org/software/trans-coord/manual/cvs/cvs.html): the ["Cederqvist" PDF](https://www.gnu.org/software/trans-coord/manual/cvs/html_node/Tracking-sources.html) is delicious lecture.
- And [SVN](https://subversion.apache.org/blog/) (under ["Vendor Branches - Chapter 4. Branching and Merging"](https://svnbook.red-bean.com/en/1.8/svn.advanced.vendorbr.html)) is almost a reference.
- [FreeBSD](https://github.com/freebsd/freebsd-doc/blob/main/documentation/content/en/articles/committers-guide/_index.adoc#54-vendor-imports-with-git) also explains the Vendor Branch concept (BTW - it uses `git subree`).

Importing external artifacts therefore requires a specific (and in this case a *simple* - believe me, it is not rocket science - as well as generic) approach: the **Vendor Branch** approach. The proposed Vendor Branch approach is KISS: *simple to apply to novice programmers*. By the way, the approach is valid in any VCS, such as SVN (where it seems to have emerged), Git, Mercurial, ClearCase, TFS, CVS (where it already exists), you name it.

Importing external artefacts in vendor branches will also provide insight in what changed in those artefacts, just by inspecting the "diff" between successive vendor versions. Normally such changes should also be announced by the vendor.

## The big picture

1. Track external changes in vendor branches
1. Do not modify anything in vendor branches
1. Updates in successive vendor artefact versions can be detected via diffs
1. ... and merged to local branches (and finally end up in your project's `main` branch)
1. Vendor tags track vendor releases
1. A local file tracks metadata about the vendor artefacts so that the vendr artefacts can always be found (until they vanish).
1. Local adaptations are likely and are applied in local branches (i.e. after merging the vendor branch).
1. Later vendor releases are imported in the vendor branch and then merged. If local changes were done, those might then generate conflicts that need to be resolved. (`git rerere` could be very helpoful, BTW).
1. Feel for contributing to the vendor code? Branch off the vendor branch, send the patch and merge locally.
1. All this needs basic Git mastery.

## As a side note: the reverse situation: you are the vendor

Interestingly, one could also use the same concept to send your code as vendor code for an outside company. You create a vendor branch from your own code and send that to the external party. That branch might sometimes need some tweaks so that you prevent things from reaching the external party. That branch serves as a reference. When changes come back you merge those changes in that vendor branch (without changing them); maybe revert some things your removed  earlier in the branch; and finally to your own `main`.

---

## HOW-TO

### 1/ define a **new vendor**:

Follow these steps in order to import external artefacts for the first time.

1. **create a vendor branch** to import the vendor (e.g. ``vendor/CELESTRAK``). If the vendor provides multiple products, you might want to create the needed subbranches instead (e.g. ``vendor/CELESTRAK/SGP4``). The "``vendor/``" segment is only meant to draw the attention to the concept.

#### If the vendor publishes via a Git repo

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

#### If the vendor does not use Git

Manually create the vendor directory to hold all vendor aretefacts. Even for *one file* your need  directory.

1. create a suitable **vendor directory** tree (e.g. ``vendor/CELESTRAK``) that will hold the external vendor artefacts. The vendor directory can also reside inside a normal directory
1. unzip/copy/import/**explode**... the external data into the (until now empty) vendor directory. This is called a *vendor drop*.
1. Make sure that extracted file/directory *names* do not contain version information as a kind of implicit versioning scheme. Rename when needed. (Example: ``geonames.2.3.tgz`` might untar into ``geonames-2.3/ ``)
1. **Commit** the vendor branch. Make sure to add at least some information on the vendor version in the commit message

	``git add -A && git commit``

#### And finalize the vendor branch creation

1. **Tag** the vendor branch, e.g. ``vendor/geonames/1.0``. A path structure allows to group tags in a more structured way. If the vendor does not provide a clear version number, use e.g. the UTC date/time of the drop, formatted as ISO: ``vendor/geonames/2021-09-09T1200`` or a date/time as stamped by the vendor.

#### Merge locally and optionally tweak

1. **Switch** to your task branch
1. **merge** the vendor branch; avoid fast forward for clarity.
`` git merge --no-ff <vendor branch>``
2. Add and commit a ``VENDOR.md`` file to ``vendor/CELESTRAK/`` that contains relevant **metadata** and instructions to help finding back the source. Consider including the keyword "VENDOR" somewhere so that it can also be grepped if needed.  Make sure to include any PDF or comparable document that documents the vendor artefact but was not included by the vendor, as well as where that PDF, data file, ... can be found.
1. Do whatever is needed to **localise** the vendor artefacts (source code, data, ...) into your project. This is done in your task branch, never in the vendor branch. Often, source code will not run or compile completely. Sometimes, data needs to be transformed; include scripts to run migrations/conversions, because future vendor drops will require them.

*Do not delete the vendor branch.*

#### Integrate
1. Have your task branch merged into your main branch. Your project now contains folowing:
	- a vendor branch and a vendor tag
	- a new vendor directory
	- optionally, modified vendor code to make the vendor work seamlessly in your project
1. **Remember to also push the *vendor* branch and tag**

### 2/ the vendor publishes a new release:
Perform a **vendor drop**.

 When the vendor publishes an update, follow these steps to ingest those changes.

1: **Switch** to the vendor branch

```
git switch <vendor branch>
```

#### ... via Git?
You are lucky.

*FIXME: markdown list item numbering issues here, due to code blocks...*

Use `git subtree pull`

```
cd `git rev-parse --show-toplevel`
git subtree pull -P <path to vendor dir> <vendor URL>
```

#### Otherwise

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

#### Finally, round up the vendor drop

5: **Tag** the vendor branch with a vendor tag (``vendor/<vendor name>/<vendor product>/<tag>``) where `<tag>` is either a tag available from the vendor, or otherwise the ISO date/time of the vendor drop or a vendor's timestamp.

### Merge

6: Merge the updated vendor branch to ``master`` (or, more probably, via an intermediate task/feature/bugfix branch, often in order to update local stuff). Run migration scripts if needed.

7: Deal with conflicts when needed. Such conflicts are expected to arise when the vendor changed something that was also changed locally, or when your project contributed to the vendor.

This is also needed when the external data disappears: in that case, mention that the external data is no longer available to the public to avoid developers searching for it (or even worse, continue with a copy that still exists elsewhere!! Such a copy does not belong on the vendor branch).

### 3/ want to contribute to the vendor project?

It may happen that you find a bug in the vendor artefacts and can provide a fix.
Here are the steps to follow.
Remember: *local modifications* are not meant to be shared with the vendor.

1. create a "contribution" branch **off a vendor tag**. Always start off an official version that is known by the vendor.
1. apply changes as necessary. merge and test locally. (this is the most git-intensive part). do not change the vendor branch itself.
1. send the contribution branch to the vendor as a pull request or patch (see Notes on patching)

#### Finally

1. merge the contribution branch to your local branches (task branch, main, ...)

### 4/ converting existing copy-pasted artefacts to their original vendor status

This is not discussed for now. You should have followed the vendor branch strategy ;-)
This is hard and requires courage; most will simply continue to dabble in their local copy.

The steps to follow *likely* include

1. create the vendor
1. rip out the old copy-pasta
1. merge the vendor branch
1. the hard part: re-apply all changes that were performed on the original copy-pasta

---

## Notes

### Binary data

Some artefacts are not available as text but in binary formats (eg [Yale Bright Star Catalog](http://tdc-www.harvard.edu/catalogs/catalogsb.html)).

Example: `iras.tar.gz` at http://tdc-www.harvard.edu/catalogs/iras.html

Remember to also include the documentation:

- http://tdc-www.harvard.edu/catalogs/iras.header.html
- http://tdc-www.harvard.edu/catalogs/iras.entry.html

*This needs to be further worked out: how to ingest HTML? As txt? Full HTML can often be ok?*

Other example: XML schema
Sometimes a format is accompanied by a metadata section describing the format of the data. This metadata is an important artefact to commit to an vendor branch. *Changes to the schema will be readily identified as diffs in the vendor branch. That's why such files must be ingested too.*

Sometimes, conversion routines need to be written or updated so that external data fits the project; maybe the data oitself is not needed, but only the generated data? These routines do not belong in the vendor branch, but in your project. 

### Patching

In some circumstances you want to also contribute to the vendor's project.

If the vendor uses a Git repo that you can access, create a pull request and skip the remainder of this section on patching.

Otherwise you need to send a patch, because your vendor directory contains totally different SHA-1 commits (that you created during the vendor drop), so the vendor's Git won't know how to apply your PR/patch.

There are a few ways to handle this situation.

#### Create a patch file with `git diff`

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

#### How to deal with binaries

If your patch adds/changes binary files, then supply those separately.

TO CONFIRM: edit the patch file, remove the references to binaries(?), and mention this to the vendor.

#### Creating a patch using `git archive`

The easiest way (also to avoid the binaries issue) is to send an entire archive *of the vendor directory* of your branch.
The vendor will then ingest that in a branch (actually they are doing a kind of vendor drop...) and run their diff/merge/etc.

```
git archive --format=tgz --output=<tarfile>.tgz <head>:<path to vendor directory> [path]
```

This creates a tgz file (run `git archive --list` for the formats) of the commit `<head>` relative to the vendor directory dirname.

Note that this even works for a single vendor file, should you have chosen not to use a directory for the vendor artefact.

### How to vendor drop a remote Git repo

Sometimes, it might be preferrable to convert a Git repo to an `git archive` in order to perform the vendor drop.

### What about `git submodules` (complex, delicate)

These are partial solutions offered by Git but do not provide the flexibility of vendor branching.
To expand further.

### What if external data is not available as text files

Sometimes, artefacts are not available as ready to ingest source code. But as a PDF that contains (parts of) source code.

Such artefacts cannot magically be turned into source code.

The idea is to store the artefact (PDF, binary file, ...) in a separate folder in the vendor branch, then painfully (yes...) extract the code into another separate folder *in the vendor branch*. And/or ask the author to provide a git repo, a file, archive, ...

If there is no tarball to explode, do not empty the vendor directory, but manually update what changed. Often this can be accomplished via scripts. (??)

And then only merge to main/master/...

### Dealing with coding style/conventions

When modifying vendor code, stick to the vendor coding style/conventions. Rewriting for readbility etc. will likely lead to massive conflicts at the next vendor drop.

### Dealing with trailing whitespace

- see also "Formatting and Whitespace" at https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration

Git tips (TO CONFIRM):

- check your current global/local/worktree whitespace handling
```
	git config --get-all --show-origin core.whitespace

	# optionally:
	git config --global core.whitespace trailing-space,-space-before-tab,indent-with-non-tab,cr-at-eol
```
- optionally, enable your repo's pre-commit hook (it checks for various whitespace issues. See also ``git help hooks``

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

### When conflicts are no longer manageable

In some cases, merging vendor updates may become too difficult if not impossible. Welcome to merge hell? Nevertheless, vendor drops will continue to provide the means to port changes, by allowing the developer to study at least what changed in the vendor branch, and apply those changes ... manually.

``git rerere`` might become helpful.

### Importing vendor code written in other languages

If the artifact is not usable without extensive rewriting (e.g. Fortran code in a C++ project), it might still make sense to vendor the original file; it will serve as an excellent reference.

This also applies for cases where explanatory/example (pseudo) sourcecode is offered; this code is essential documentation that the vendor might update occasionally.

### Version information in vendor file/directory paths

Sometimes, developers add directories/files with path names containing version information. An example is the FAQ file of [this landscape](http://www.alienbasecamp.com/Stellarium/sun.htm).

This is a bad practice, usually done by developers that do not have access to a good VCS. (Note also the CR/LF issue.)

In such cases, the version information should be removed before committing the vendor drop; this will present the additional advantage that the history of such a file becomes available. This is a delicate step, as the "pristine copy" requirement is no longer fulfilled. If filename information is part of the correct functioning of the imported artifacts (e.g. it is also referenced in vendor scripts, in linked data, etc), then you are facing a difficult problem. In such a case (for now), it might be better to not rename the vendor file paths at all and accept that file history must be discovered by other means (e.g. a manual diff). *TODO find a better way here.* Note that Git might be able to find out what changed in renamed files.

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
- this topic probably contains many more examples but is clouded by unrelated topics: https://github.com/topics/vendor; if possible, try to convince those repos to change to the more focused `vendor-branching` tag.

### Copy-pasta
- https://en.wikipedia.org/wiki/Software_rot
- https://en.wikipedia.org/wiki/Copypasta#Technology
- "*[copy-paste is evil](https://stackoverflow.com/questions/2490884/why-is-copy-and-paste-of-code-dangerous)*"
- https://www.cisa.gov/resources-tools/resources/product-security-bad-practices: _"Cache copies of all open-source dependencies within the manufacturer’s own build systems and do not update products or customer systems directly from unverified public sources."_ (Dead link, see instead https://github.com/cisagov/bad-practices/discussions/101)

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

### To be evaluated:
- a [rebasing vendor branch workflow](https://talesfromthearmchair.net/a-rebasing-vendor-branch-workflow-for.html)
	- if you read this far, we need to grok that
	- looks related to [FreeBSD 5.4.6. Rebasing your change against latest FreeBSD source tree](https://github.com/freebsd/freebsd-doc/blob/main/documentation/content/en/articles/committers-guide/_index.adoc#546-rebasing-your-change-against-latest-freebsd-source-tree)
- https://github.com/thejoshwolfe/git-vendor
	- a script to automate vendor branching:
	- makes a comparison with submodule, subtree, brettlangdon
- (Dead?) https://github.com/brettlangdon/git-vendor
- how to deal with huge repos, such as the migration of TLE to spacedatastandards OMM: their repo is too big for the average developer to ingest *as a vendor branch*.

## Links

- https://github.com/topics/vendor-branching
- https://github.com/axd1967/SGP4-vendor-branching
- https://github.com/axd1967/stellarium/blob/contrib/docs/vendor-branching/VENDOR_BRANCHING.md

