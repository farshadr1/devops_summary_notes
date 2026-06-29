# Git Commands Cheat Sheet

## 🔧 SETUP & CONFIGURATION
```bash
git config --global user.name "Your Name"          # Set username
git config --global user.email "email@example.com" # Set email
git config --list                                  # View all configs
git config --global --edit                         # Edit config file
```

## 🚀 STARTING A REPOSITORY
```bash
git init                                           # Initialize new repo
git clone <url>                                    # Clone remote repo
git clone <url> <directory>                        # Clone to specific folder
```

## 📝 MAKING CHANGES
```bash
git status                                         # Show working directory status
git add <file>                                     # Stage specific file
git add .                                          # Stage all changes
git add -A                                         # Stage all (including deletions)
git rm --cached <file>                             # Remove from staging only
```

## 💾 COMMITTING CHANGES
```bash
git commit -m "message"                            # Commit with message
git commit -am "message"                           # Add all tracked files & commit in one step
```

## 🌿 BRANCHING & MERGING
```bash
git branch                                         # List branches
git branch <name>                                  # Create branch
git branch -d <name>                               # Delete branch
git branch -D <name>                               # Force delete branch
git branch -m <old> <new>                          # Rename branch
git checkout <branch>                              # Switch branch
git switch <branch>                                # Switch branch (newer)
git merge <branch>                                 # Merge branch into current
git merge --abort                                  # Abort merge conflict
git merge --squash <branch>                        # Merge as single commit
git rebase <branch>                                # Rebase current branch
```

## 🔄 REMOTE REPOSITORIES
```bash
git remote -v                                      # List remote repos
git remote add <name> <url>                        # Add remote
git remote remove <name>                           # Remove remote
git remote rename <old> <new>                      # Rename remote
git remote show <name>                             # Show remote info
git fetch                                          # Fetch all remotes
git pull                                           # Fetch + merge
git pull --rebase                                  # Fetch + rebase
git push                                           # Push to remote
git push <remote> <branch>                         # Push specific branch
git push -u <remote> <branch>                      # Push & set upstream
git push --force                                   # Force push (use with caution!)
git push --force-with-lease                        # Safer force push
git push --tags                                    # Push tags
```

## 📜 VIEWING HISTORY
```bash
git log                                            # Show commit history
git log --oneline                                  # Compact history
git log --graph                                    # Show branch graph
git log --oneline --graph --all                    # Full graph view
git log -p                                         # Show diffs
git log -<n>                                       # Limit to n commits
git log --author="name"                            # Filter by author
git log --since="1 week ago"                       # Filter by date
git log --grep="pattern"                           # Search commit messages
git log <file>                                     # History for specific file
git show <commit>                                  # Show commit details
git diff                                           # Show unstaged changes
git diff --staged                                  # Show staged changes
git diff <branch1> <branch2>                       # Compare branches
git diff <commit1> <commit2>                       # Compare commits
git diff HEAD                                      # Compare with n commits ago
```

## ⏪ UNDOING CHANGES
```bash
git restore <file>                                 # Undo Changes in Working Directory 
git restore --staged <file>                        # Unstage Files + keep files
git reset HEAD~<n>                                 # Uncommit + unstage + keep files
git reset --hard HEAD~<n>                          # Uncommit + discard changes
git reset --hard <commit>                          # Reset to specific commit
git revert <commit>                                # Create undo commit
git revert HEAD                                    # Undo a pushed commit
```
### Easy Rule: 
```
Not committed?  →  git restore

Committed but NOT pushed?  →  git reset

Already pushed?  →  git revert
```

### The Three Trees of Git:

Tree	                |Location	        |What it is	                    |Command to 
------------------------|-------------------|-------------------------------|----------
Working Directory	    |Your computer	    |Actual files you're editing	|ls / cat
Staging Area (Index)	|.git/index	        |Files ready to commit	        |git status
Commit History	        |.git/objects	    |Saved snapshots	            |git log

### HEAD~n :

A ← B ← C ← D (HEAD)

| Expression | Meaning                |
| ---------- | ---------------------- |
| `HEAD`     | Current commit (D)     |
| `HEAD~1`   | One commit back (C)    |
| `HEAD~2`   | Two commits back (B)   |
| `HEAD~3`   | Three commits back (A) |


## 🏷️ TAGS
```bash
git tag                                            # List tags
git tag <name>                                     # Create lightweight tag
git tag -a <name> -m "message"                    # Create annotated tag
git tag -d <name>                                  # Delete tag
git push --tags                                    # Push all tags
git show <tag>                                     # Show tag details
```

🧹 CLEANING & MAINTENANCE
bash

git clean -n                                       # Show what would be removed
git clean -f                                       # Remove untracked files
git clean -fd                                      # Remove untracked files & dirs
git clean -fx                                      # Remove ignored files too
git gc                                             # Garbage collection
git fsck                                           # Filesystem integrity check
git reflog                                         # Show reference log
git reflog expire --expire=now --all               # Clear reflog

🔍 SEARCHING
bash

git grep "pattern"                                 # Search code
git grep -n "pattern"                              # With line numbers
git grep -c "pattern"                              # Count occurrences
git log -S "text"                                  # Search in code changes
git log -G "regex"                                 # Search with regex
git rev-list --all --grep="pattern"                # Search commits

🎯 WORKING WITH SUBMODULES
bash

git submodule add <url> <path>                     # Add submodule
git submodule update --init                        # Clone submodules
git submodule update --remote                      # Update submodules
git submodule foreach git pull                     # Update all submodules
git submodule deinit <path>                        # Remove submodule

⚡ USEFUL SHORTCUTS & ALIASES
bash

# Set aliases
git config --global alias.s status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --date=short"

# Then use: git s, git co, git br, git lg, etc.

📊 STATISTICS & INSPECTION
bash

git shortlog -sn                                   # Contributor commit count
git shortlog -sn --all                             # All branches
git ls-files                                       # List tracked files
git ls-tree -r HEAD                                # List files in commit
git count-objects -v                               # Repository size stats
git rev-list --count HEAD                          # Total commit count
git diff --stat                                    # Show change statistics
git diff --shortstat                               # Compact stats

🚨 FIXING MISTAKES
bash

# Undo a push (create revert commit)
git revert HEAD --no-edit && git push

# Change author of last commit
git commit --amend --author="Name <email>"

# Change author of multiple commits
git rebase -i HEAD~<n>  # Mark commits as 'edit', then:
git commit --amend --author="Name <email>" --no-edit
git rebase --continue

# Recover deleted branch
git reflog                         # Find commit hash
git checkout -b <branch> <commit>  # Recreate branch

# Detach HEAD - go back to previous
git switch -  # or
git checkout -

🌐 WORKING WITH REMOTE BRANCHES
bash

git checkout -b <branch> origin/<branch>           # Track remote branch
git branch --set-upstream-to=origin/<branch>       # Set tracking
git branch -vv                                     # Show tracking branches
git remote prune origin                            # Remove stale remote branches
git fetch --prune                                  # Fetch and cleanup

📦 ARCHIVE & EXPORT
bash

git archive --format=zip HEAD > archive.zip        # Create zip archive
git archive --format=tar HEAD | gzip > archive.tar.gz # Tar.gz archive
git archive --format=zip -o archive.zip HEAD       # Alternative

🎯 PRO TIPS

    Use git add -p for interactive staging (review changes before adding)

    Use git commit -v to see diff in commit message editor

    Use git fetch --all --prune to sync all remotes and cleanup

    Use git log --follow <file> to see file history across renames

    Use git diff HEAD^ to compare with previous commit

    Use git rebase -i HEAD~3 to squash, reword, or reorder commits

⚠️ WARNING

    git reset --hard permanently removes changes - use with caution!

    git push --force can overwrite remote history - use --force-with-lease instead

    Always backup before using git clean -f or git gc