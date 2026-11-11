# Notes

The more gritty things...

## Binary data

Some artefacts are not available as text but in binary formats (eg [Yale Bright Star Catalog](http://tdc-www.harvard.edu/catalogs/catalogsb.html)).

Example: `iras.tar.gz` at http://tdc-www.harvard.edu/catalogs/iras.html

Remember to also include the documentation:

- http://tdc-www.harvard.edu/catalogs/iras.header.html
- http://tdc-www.harvard.edu/catalogs/iras.entry.html

*This needs to be further worked out: how to ingest HTML? As txt? Full HTML can often be ok?*

Other example: XML schema
Sometimes a format is accompanied by a metadata section describing the format of the data. This metadata is an important artefact to commit to an vendor branch. *Changes to the schema will be readily identified as diffs in the vendor branch. That's why such files must be ingested too.*

Sometimes, conversion routines need to be written or updated so that external data fits the project; maybe the data oitself is not needed, but only the generated data? These routines do not belong in the vendor branch, but in your project. 

## Patching

In some circumstances you want to also contribute to the vendor's project.

If the vendor uses a Git repo that you can access, create a pull request and skip the remainder of this section on patching.

Otherwise you need to send a patch, because your vendor directory contains totally different SHA-1 commits (that you created during the vendor drop), so the vendor's Git won't know how to apply your PR/patch.

There are a few ways to handle this situation.

### Create a patch file with `git diff`

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

### How to deal with binaries

If your patch adds/changes binary files, then supply those separately.

TO CONFIRM: edit the patch file, remove the references to binaries(?), and mention this to the vendor.

### Creating a patch using `git archive`

The easiest way (also to avoid the binaries issue) is to send an entire archive *of the vendor directory* of your branch.
The vendor will then ingest that in a branch (actually they are doing a kind of vendor drop...) and run their diff/merge/etc.

```
git archive --format=tgz --output=<tarfile>.tgz <head>:<path to vendor directory> [path]
```

This creates a tgz file (run `git archive --list` for the formats) of the commit `<head>` relative to the vendor directory dirname.

Note that this even works for a single vendor file, should you have chosen not to use a directory for the vendor artefact.

## How to vendor drop a remote Git repo

Sometimes, it might be preferrable to convert a Git repo to an `git archive` in order to perform the vendor drop.

## What about `git submodules` (complex, delicate)

These are partial solutions offered by Git but do not provide the flexibility of vendor branching.
To expand further.

## What if external data is not available as text files

Sometimes, artefacts are not available as ready to ingest source code. But as a PDF that contains (parts of) source code.

Such artefacts cannot magically be turned into source code.

The idea is to store the artefact (PDF, binary file, ...) in a separate folder in the vendor branch, then painfully (yes...) extract the code into another separate folder *in the vendor branch*. And/or ask the author to provide a git repo, a file, archive, ...

If there is no tarball to explode, do not empty the vendor directory, but manually update what changed. Often this can be accomplished via scripts. (??)

And then only merge to main/master/...

## When conflicts are no longer manageable

In some cases, merging vendor updates may become too difficult if not impossible. Welcome to merge hell? Nevertheless, vendor drops will continue to provide the means to port changes, by allowing the developer to study at least what changed in the vendor branch, and apply those changes ... manually.

``git rerere`` might become helpful.

## Importing vendor code written in other languages

If the artifact is not usable without extensive rewriting (e.g. Fortran code in a C++ project), it might still make sense to vendor the original file; it will serve as an excellent reference.

This also applies for cases where explanatory/example (pseudo) sourcecode is offered; this code is essential documentation that the vendor might update occasionally.

## Version information in vendor file/directory paths

Sometimes, developers add directories/files with path names containing version information. An example is the FAQ file of [this landscape](http://www.alienbasecamp.com/Stellarium/sun.htm).

This is a bad practice, usually done by developers that do not have access to a good VCS. (Note also the CR/LF issue.)

In such cases, the version information should be removed before committing the vendor drop; this will present the additional advantage that the history of such a file becomes available. This is a delicate step, as the "pristine copy" requirement is no longer fulfilled. If filename information is part of the correct functioning of the imported artifacts (e.g. it is also referenced in vendor scripts, in linked data, etc), then you are facing a difficult problem. In such a case (for now), it might be better to not rename the vendor file paths at all and accept that file history must be discovered by other means (e.g. a manual diff). *TODO find a better way here.* Note that Git might be able to find out what changed in renamed files.

## To be evaluated:

- https://stackoverflow.com/ai-assist/shared/4adf31b5-4c07-4b3b-9fc1-61d89536a4b7
	- impact on diff
- a [rebasing vendor branch workflow](https://talesfromthearmchair.net/a-rebasing-vendor-branch-workflow-for.html)
	- if you read this far, we need to grok that
	- because rebasing might give issues.
	- looks very similar to [FreeBSD 5.4.6. Rebasing your change against latest FreeBSD source tree](https://github.com/freebsd/freebsd-doc/blob/main/documentation/content/en/articles/committers-guide/_index.adoc#546-rebasing-your-change-against-latest-freebsd-source-tree)
- https://github.com/thejoshwolfe/git-vendor
	- a script to automate vendor branching
	- makes a comparison with submodule, subtree, brettlangdon (which is dead?)
	- maybe too complicated?
- how to deal with huge repos, 
	-such as the migration of TLE format to spacedatastandards OMM (see [SGP4](cases/SGP4/README.md): their repo is too big for the average developer to ingest *as a vendor branch*.
	- git lfs
	- a very interesting approach is to make a mirror of the vendor, apply local changes, and import the branch with the modified files "somehow" (not sure [CPM](https://github.com/cpm-cmake/CPM.cmake/discussions/720) is needed) in the local project is needed. Pity submodules must live inside their superprojects.
	- note that the Git SCM book also explains [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) as a solution: "The issue with copying the code into your own project [without using submodules] is that any custom changes you make are difficult to merge when upstream changes become available.". But the whole concept feels heavy.
- helping decide what license may or may not allow vendor branching
- https://pip.pypa.io/en/stable/development/vendoring-policy/
- https://pip.pypa.io/en/stable/cli/pip_wheel/

