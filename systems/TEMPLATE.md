# Vendor Branching using XXX

<!-- 
	1. copy as a new page XXX.md
	2. replace XXX with your favourite VCS name: CVS, SVN, ... 
-->

The following sequence is almost identical for creating as well as updating a vendor and could serve as a compact generic checlist too.

<!-- remove sections that do not apply --> 

## . switch to vendor branch

```
create (if possible orphan) vendor branch
switch
```

For the "why" of the orphan branch, see the [Git page](GIT.md).

## . empty vendor directory

```
cd <vendor dir>
git ls-files -z | xargs -0 rm -f
```

## .. vendor drop (vendor uses XXX)

<!-- using `git subtree` as example. Maybe XXX does not have similar provision -->
```
git subtree add / pull --prefix=<path to vendor dir> <vendor URL> <vendor Git ref>
```

## .. "ALIEN" vendor drop (vendor does not use XXX)

```
tar -xzf vendor.tgz
```
Rename the directory if it contains version names.

## . commit & tag the vendor branch

```
commit the vendor branch
tag <vendor branch> <vendor tag>
```

## . switch to task branch

```
switch taskbranch
```

## . merge vendor branch

```
merge vendor branch
handle conflicts
```

## . update VENDOR file in the vendor directory

```
edit <vendor dir path>/VENDOR.md
```

`VENDOR.md` could also be a `VENDOR.sh` file that executes a vendor drop.

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

- Monitor changes to this page so that you can apply the changes to your page.

