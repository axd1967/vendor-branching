# Various Git notes

## What about Git submodules?

Disclaimer: Personally I have never used them, and the Internet is not recomforting. But they do deserve a study.

Submodules are a bit hairy to deal with; the concept feels heavy.

To expand further.

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
- git lfs
- `git subtree`:
	- https://apenwarr.ca/log/20090430-a-new-alternative-to-git-submodules-git-subtree
	- https://github.com/git/git/blob/master/contrib/subtree/git-subtree.adoc
	- https://david.rothlis.net/vendor-branch/
	- https://www.atlassian.com/git/tutorials/git-subtree
- https://github.com/git/git/blob/master/Documentation/technical/unit-tests.adoc
- how to correctly vendor drop: https://github.com/git/git/blob/master/Documentation/git-rm.adoc#using-git-add--a

