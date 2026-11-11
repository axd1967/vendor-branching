# Various git tips

## Dealing with coding style/conventions

When modifying vendor code, stick to the vendor coding style/conventions. Rewriting for readbility etc. will likely lead to massive conflicts at the next vendor drop.

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

# See also

- ``git help config``
- https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration
- https://adaptivepatchwork.com/2012/03/01/mind-the-end-of-your-line/
- https://docs.github.com/en/get-started/git-basics/configuring-git-to-handle-line-endings

