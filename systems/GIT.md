# Vendor Branching using Git

The following sequence is almost identical for creating as well as updating a vendor.

## . switch to vendor branch

```
git switch [--orphan=]<vendor branch> 
# if you used the orphan option tro create the branch, you will notice that at this point your project directory is now empty

# only for the first commit
git commit --allow-empty -m "create vendor XXX"
```

- The vendor branch is initially unrelated to your own repository, therefore it must start as an orphan.
- You need an empty commit.

## .. vendor drop - vendor uses Git

```
git subtree add (when creating) / pull (when updating) --prefix=<path to vendor dir> <vendor URL> <vendor Git ref>
```

Note that `git subtree` does not remember the vendor information, so that must be recorded in the upcoming VENDOR file. Also, '.' cannot be used as prefix.
Note about `git subtree --squash`:

- avoid it when you intend to contribute back to the vendor. *We all want that, right?* 
- use it to reduce disk space

## .. ALIEN vendor drop - vendor does not use Git

```
cd <vendor dir>
git ls-files -z | xargs -0 rm -f

tar -xzf vendor.tgz
git add -A
```

## . commit & tag the vendor branch

```
# only if you didn't use `git subtree`:
git commit -m "import vendor XXX version YYY"

# tag
git tag <vendor tag> <vendor branch>
```

## . switch to task branch

```
git switch taskbranch
```

## . merge vendor branch

```
git merge --allow-unrelated-histories --no-ff <vendor tag>
```

`--allow-unrelated-histories` otherwise first vendor merge won't work.
Use the vendor tag. It makes the vendor drop explicit.

Notice how the log shows how the orphan branch rejoined the main branch. This can best be seen in the [network page of the GitLab fork](https://gitlab.com/axd/vendor-branching/-/network/master?ref_type=heads).

## . update VENDOR file

```
edit VENDOR.md
```

Remember to mention at the very least the vendor URL 

## . tweak vendor code

```
hack away
```

## . merge to main branch

```
switch main
merge task branch
```

# Various Git notes

## What about Git submodules?

Disclaimer: Personally I have never used them, and the Internet is not recomforting. But they do deserve a study.

Submodules are a bit hairy to deal with; the concept feels heavy.

To expand further.

- https://stackoverflow.com/questions/31871888/what-goes-wrong-when-using-git-worktree-with-git-submodules
- note that the Git SCM book also explains [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) as a solution: *"The issue with copying the code into your own project [without using submodules] is that any custom changes you make are difficult to merge when upstream changes become available."*.
- https://git-scm.com/docs/gitsubmodules
- https://git-scm.com/docs/git-submodule
- https://git-scm.com/docs/gitmodules
- https://github.com/git/git/blob/master/Documentation/user-manual.adoc#submodules
- https://gitprotect.io/blog/managing-git-projects-git-subtree-vs-submodule/
- https://github.com/apenwarr/git-subtrac mentions issues when using subtree

## Dealing with trailing whitespace

- see also "Formatting and Whitespace" at https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration

Git tips (TO CONFIRM and maybe to move out of this file):

- a Git trick to go to to GITROOT:  cd `git rev-parse --show-toplevel` 
- gitignore rules should relate to project files only, not developer habits. e.g. a `[Dd]esktop.ini` pattern does belongs in `~/.gitignore`
- check your current global/local/worktree whitespace handling
```
	git config --get-all --show-origin core.whitespace

	# optionally:
	git config --global core.whitespace trailing-space,-space-before-tab,indent-with-non-tab,cr-at-eol
```
- optionally, enable your repo's pre-commit hook (it checks for various whitespace issues. See also ``git help hooks``

## ...and platform end of line issues

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

## todo

- https://github.com/thejoshwolfe/git-vendor
	- a script to automate vendor branching
	- an interesting and extensive discussion of alternatives: makes a comparison with submodule, subtree, brettlangdon (which is dead?)
	- can the `subdir` option deal with large vendors?
	- maybe too complicated?
	- **but... it looks orphaned. a typical (and ironic) issue**
- git lfs
- `git subtree`:
	- https://apenwarr.ca/log/20090430-a-new-alternative-to-git-submodules-git-subtree
	- https://github.com/git/git/blob/master/contrib/subtree/git-subtree.adoc
	- https://david.rothlis.net/vendor-branch/
	- https://www.atlassian.com/git/tutorials/git-subtree
- https://github.com/git/git/blob/master/Documentation/technical/unit-tests.adoc
- how to correctly vendor drop: https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a

see ``git help git-rm`` for details, search for "[vendor](https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a)". *Do notice BTW that the concept is mentioned in Git*
- https://git-man-page-generator.lokaltog.net/

https://sfconservancy.org/GiveUpGitHub/
https://giveupgithub.org/
https://en.wikipedia.org/wiki/Embrace,_extend,_and_extinguish

