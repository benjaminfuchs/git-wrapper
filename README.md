git-wrapper
===========

This script allows you to wrap git and implement **pre-** and **post-command** hooks. Put this script in your **PATH** variable before your acutal git executable.

Along with the command hook infrastructure it offers right now three differnt features:
* Forbid to commit in a detached head state.
* Warn you if your submodules got out-dated by a pull/checkout/merge.
* Pass through a submodule commit to the parent repository.

See the following example outputs to see what the features actual do.


### Error when commit in a detached head state

```
sub $ git commit
[git-wrapper] ERROR: HEAD detached. You have to switch to a branch before you can commit.
[git-wrapper]   (use "git branch <branch-name>")
[git-wrapper] Disable this check by setting GIT_WRAPPER_NOPRECOMMIT to a nonempty value.
sub $
```

### Warning if your submodules got out-dated
```
parent $ git pull origin master 
From ...
 * branch            master     -> FETCH_HEAD
Fetching submodule sub
Already up-to-date.

[git-wrapper] WARNING: One or more submodules do not match the SHA-1 found in the index
[git-wrapper]   (use "git submodule update" to checkout the submodules)
[git-wrapper] Disable this check by setting GIT_WRAPPER_NOPOSTCHECKOUT to a nonempty value.
parent $
```

```
parent $ git checkout master
M	sub
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.

[git-wrapper] WARNING: One or more submodules do not match the SHA-1 found in the index
[git-wrapper]   (use "git submodule update" to checkout the submodules)
[git-wrapper] Disable this check by setting GIT_WRAPPER_NOPOSTCHECKOUT to a nonempty value.
parent $
```

### Passing through a submodule commit to the parent repository
``` 
sub $ git commit -am "Commit to submodule and parent"
[master 59fcf9c] Commit to submodule and parent
 1 file changed, 1 insertion(+)

[git-wrapper] You just commited to the submodule: 'sub'
[git-wrapper] Do you wish to update the parent repo to this commit?
[git-wrapper] name:   parent
[git-wrapper] branch: master
[git-wrapper] (set GIT_WRAPPER_NOPOSTCOMMIT to a nonempty value to never ask again)
[git-wrapper] This will require to push your changes first. [y/n] y

[git-wrapper] pushing submodule...
+ git push origin master
To ...
 * [new branch]      master -> master

[git-wrapper] commiting parent...
+ git -C /some/dir commit -m '[sub] Commit to submodule and parent' -- sub
[master b382cf9] [sub] Commit to submodule and parent
 1 file changed, 1 insertion(+), 1 deletion(-)

[git-wrapper] pushing parent...
+ git -C /some/dir push origin master
To ...
   d79ed78..b382cf9  master -> master
sub $ 
```
