# github command line interface

## instalation
```bash
sudo apt update
sudo apt install gh
```

## profile commands
```bash
gh auth login
gh auth logout
gh auth status
```

## for Codespace auth problems
when we create a blank codespace, there is conflict in auth. 
better to Create the Codespace from the repository itself 
```bash
echo 'unset GITHUB_TOKEN' >> ~/.bashrc
```