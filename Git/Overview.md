# Overview

Overriding history
``` shell
# Checkout
git checkout --orphan <NEW_BRANCH>

# Add all the files
git add -A

# Commit the changes
git commit -am "commit message"

# Delete the original branch
git branch -D <BRANCH>

# Rename the current branch to original branch name
git branch -m <BRANCH>

# Finally, force update your repository
git push -f <REMOTE> <BRANCH>

# Remove the old files
git gc --aggressive --prune=all
```

Get remote information
```shell
git remote show <REMOTE>
```