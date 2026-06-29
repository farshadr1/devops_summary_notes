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