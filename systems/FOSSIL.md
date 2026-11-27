# Vendor Branching using Fossil

<!--

With thanks

- https://fossil-scm.org/forum/info/8860d29c5a42e259d79d
- https://fossil.craftdesign.group/fossil-examples/wiki?name=Vendor+branches

(using fossil 2.28)

-->

<!-- 
	A VENDOR file or script could define following variables:
	
	REPO=vendor-branching
	VENDOR=axd1967/$REPO
	URL=https://github.com/$VENDOR
	VERSION=master # this should be an argument to the script

	ZIPFILE=$VERSION.zip
	ZIPFILE_URL=$URL/archive/refs/heads/$ZIPFILE

	LOCAL_VENDOR_DIR=vendor/$VENDOR
	VENDOR_BRANCH=vendor/$VENDOR/source # to distinguish from /contrib branches
	TAG=vendor/$VENDOR/$VERSION

	look also for the Fossil VENDOR.sh script!
-->

NOTE: Fossil recommends to create the branch at commit time

## . switch to vendor branch

```
# create or switch to vendor branch

fossil branch new $VENDOR_BRANCH
fossil update $VENDOR_BRANCH
```

## . empty vendor directory

```
mkdir -p $LOCAL_VENDOR_DIR
cd $LOCAL_VENDOR_DIR
rm -rfI *
```

## .. "sibling" vendor drop (i.e. vendor uses Fossil)

TODO
<!-- using `git subtree` as example. Maybe Fossil does not have similar provision, the the "alien" drop might be the alternative -->
```
TODO git subtree add / pull --prefix=$LOCAL_VENDOR_DIR $URL $VERSION
```


## .. "alien" vendor drop (vendor does not use Fossil)

```
wget $ZIPFILE
unzip $ZIPFILE
etc

# NOTE/TODO: 
# Rename the directory if it contains version names!

fossil addremove --dotfiles
```

## . commit & tag the vendor branch

```
fossil commit --tag $TAG --branch $VENDOR_BRANCH -m "Import $VERSION from $VENDOR as $TAG"
```

## . switch to your task branch

```
fossil update taskbranch
```

## . merge vendor branch

```
fossil merge $TAG
```

## . update VENDOR file in the vendor directory

```
edit $LOCAL_VENDOR_DIR/VENDOR.md # (unless VENDOR.sh runs the vendor drop)
```

`VENDOR.md` could also be a `VENDOR.sh` file that executes a vendor drop. 
As the next vendor drops are similar, a script is recommended! 
See [this Fossil example](https://chiselapp.com/user/axd/repository/vendor-branching/file?name=vendor/axd1967/vendor-branching/VENDOR.sh)

## . tweak vendor code

```
hack away
```

## . merge to main branch

```
fossil update trunk
merge task branch
```

## . publish

Fossil should autosync everything...

```

```

# Notes (if any)

- Monitor changes to [this template page](https://github.com/axd1967/vendor-branching/blob/master/systems/TEMPLATE.md) so that you can apply the changes to your page.

