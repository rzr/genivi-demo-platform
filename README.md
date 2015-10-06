## GENIVI Demo Platform Project

This is a full setup of Yocto GENIVI Demo Platform (GDP) using [git
submodules](https://www.kernel.org/pub/software/scm/git/docs/git-submodule.html)
to pull in dependencies.

The submodules setup defines all the required yocto/bitbake layers and the
exact version (commit hash) that should be used for each one. It therefore
replaces instructions to manually clone each layer, and checkout particular
commit hash values.

In addition to defining submodules, this repository includes a helper
script and the gdp-src-build/conf directory that includes the bitbake
configuration files.  The needed submodules and the configuration files are
different on each branch.  That way a branch of this parent project
completely defines the build for a particular version and hardware.

To get all the submodules in one go it is recommended to :

```bash
git clone --recursive <thisrepo> -b <branch>
```
where branch is (for example) qemux86-64, koelsch, porter or minnowboard
(you must check which branches exist)

Depending on which hardware (which branch) is selected, the layers that
provide the unique configuration and the hardware dependent BSP will be
adjusted accordingly.

Development note:

There is no master branch, but "qemux86-64" could be considered the base
branch on which common changes should be added and then merged to other
branches.  All standard branches should be in a working state, although
branches starting with "dev" could be more experimental.

### Build instructions
```
source init.sh
```
and then follow instructions.

That's it! 

The rest of this README is only needed for advanced usage / development.

### Switching branches

To switch branch (or if you forgot the recursive option during clone), try:
```bash
git checkout <branch>
git submodule init
git submodule update
```

### More information on submodule updates (development)

Submodule update is included in the init.sh script, and therefore a normal
user usually does not need to do any more but for clarity here is a short
description of what happens:

If you decide to switch branches (git checkout <branch>), then submodules
are adjusted according to what the branch defines but the content inside
each submodule is not adjusted by checkout. "submodule update" will proceed
to checkout the correct commit inside each submodule that this particular
project configuration requires.

Specifically, git will *add* new submodules and fill their content during
checkout+update.  Note however that git does not really remove directories
with content here.  A submodule that is not included in the new
configuration (branch) will be left in the filesystem by git - it is simply
not considered to be a submodule any longer.  Git will therefore show this
content like it any local changes in the repository (i.e. "untracked
files"). If you want to really remove a layer after switching branches, you
need to do it manually (but it does no harm if it stays since the
bitbake conf/ files are no longer referencing that layer in the 
build definition)

Reminder: submodule update is already included in init.sh so you often
don't need to do it manually.

Beware: (development): If you have committed new changes in any layer (submodule) but not yet
committed the new submodule version to the parent project, then be careful
with submodule update since it will reset the submodule to to the "fixed"
commit defined in the parent project.

*Untracked* changes inside a submodule are not removed - very few git
commands are unsafe when it comes to not yet tracked files - but if you have
committed the changes, the submodule will be reset to the commit defined by
the parent project.  If it happens you have to go back inside the submodule
and reset it back to the newer commit you created.  Then either commit the
submodule SHA change in the parent project, or avoid submodule update
(which is in init.sh) and simply bitbake to try your changes.

If this does not make sense or you do anything more advanced you probably
need to study information on git submodules - they are a bit special to
work with...

One recommendation to improve usability a little: 
```bash
git config diff.submodule log
```

Try git diff in the parent project, with/without this setting to see the
difference.

