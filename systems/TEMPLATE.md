# Vendor Branching using XXX

<!-- 
	1. copy as a new page systems/XXX.md
	2. replace XXX with your favourite VCS name: CVS, SVN, ... 
	3. use https://github.com/axd1967/vendor-branching as an example vendor drop!

	A script could use following variables:
	
	VENDOR = axd1967/vendor-branching
	URL = https://github.com/$VENDOR
	VERSION = master

	ZIPFILE = $URL/archive/refs/heads/$VERSION.zip
	VENDOR_BRANCH = vendor/$VENDOR
	TAG = vendor/$VENDOR/$VERSION
	LOCAL_VENDOR_DIR = vendor/$VENDOR
-->

The following sequence is almost identical for creating as well as updating a vendor and could serve as a compact generic checlist too.

<!-- remove sections that might not apply --> 

## . switch to vendor branch

```
# create (if possible orphan) or switch to vendor branch
switch --orphan $VENDOR_BRANCH
switch --create $VENDOR_BRANCH
switch $VENDOR_BRANCH
```

For the "why" of the orphan branch, see the [Git page](GIT.md).

## . empty vendor directory

```
cd $LOCAL_VENDOR_DIR
git ls-files -z | xargs -0 rm -f
```

## .. "sibling" vendor drop (i.e. vendor uses XXX)

<!-- using `git subtree` as example. Maybe XXX does not have similar provision, the the "alien" drop might be the alternative -->
```
git subtree add / pull --prefix=$LOCAL_VENDOR_DIR $URL $VERSION
```

## .. "alien" vendor drop (vendor does not use XXX)

```
unzip $ZIPFILE
```
Rename the directory if it contains version names!

## . commit & tag the vendor branch

```
commit $VENDOR_BRANCH
tag $VENDOR_BRANCH $TAG
```

## . switch to your task branch

```
switch <taskbranch>
```

## . merge vendor branch

```
merge $VENDOR_BRANCH
handle conflicts...
```

## . update VENDOR file in the vendor directory

```
edit $LOCAL_VENDOR_DIR/VENDOR.md
```

`VENDOR.md` could also be a `VENDOR.sh` file that executes a vendor drop. 
As the next vendor drops are similar, a script is recommended! 
See the example in [GitLab](https://gitlab.com/axd/vendor-branching/-/blob/master/vendor/vendor-branching/VENDOR.sh)

## . tweak vendor code

```
hack away
```

## . merge to main branch

```
switch main
merge task branch
```

## . publish
```
push main branch
push vendor branch
push vendor tag
```

# Notes (if any)

- Monitor changes to [this template page](https://github.com/axd1967/vendor-branching/blob/master/systems/TEMPLATE.md) so that you can apply the changes to your page.

