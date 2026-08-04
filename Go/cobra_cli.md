## Install Cobra

```bash
# First, initialize a new Go module for your project:
mkdir my-cli
cd my-cli
go mod init my-cli

# Download and Install the Cobra library: (-u: Update all its dependencies too)
go get -u github.com/spf13/cobra@latest

# Install the Cobra CLI generator:
go install github.com/spf13/cobra-cli@latest

# Initialize your Cobra project:
cobra-cli init

# Add Commands to /cmd
cobra-cli add <command>

# Run code
go build -o my-
./my-cli <command>
```
