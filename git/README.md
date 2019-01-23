# Git

## Daily Tasks

```bash
# Work in a branch
git checkout -b my-branch
git add *
git commit -m "Reason"
git push -u origin my-branch

# Check history
git log --oneline --graph
git log --oneline --graph --simplify-by-decoration --all
```

## Submodules

```bash
# Add submodule
git submodule add https://github.com/me/myrepo.git {submodule directory}

# Update a submodule to latest commit (i.e. record the commit SHA1 in the parent project index)
cd {submodule directory}
git checkout master
git pull

# View commit of submodules
git submodule status
```

To work directly in a submodule:

```bash
# 1. Specify branch in .gitmodules
branch = the-branch-name

# 2. Update the submodule repo
git submodule update [--remote]

# 3. Remove branch name from .gitmodules

# 4. Make changes in submodule repo
git add *
git commit -m "The reason"
git push origin HEAD:the-branch-name

# 5. Commit change on parent project
```
