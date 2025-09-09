
---
# Shell Scripting

### **Philosophy of Shell Scripting in DevOps**
The goal isn't to write complex programs in shell. The goal is to **glue together** commands, tools, and applications (like Docker, Kubernetes, AWS CLI, Terraform) to automate processes reliably.

---

### **1. The Absolute Basics**

#### **a) The Shebang (`#!/bin/bash`)**
Every script must start with this. It tells the system which interpreter to use to run the script.

```bash
#!/bin/bash
```

For better portability, use `#!/usr/bin/env bash`.

#### **b) Making a Script Executable**
```bash
# 1. Create the file
vim my_script.sh

# 2. Add the shebang and commands (see below)
# 3. Make it executable
chmod +x my_script.sh

# 4. Run it
./my_script.sh
```

#### **c) Your First Script**
```bash
#!/bin/bash
# This is a comment
echo "Hello, World! The current date is: $(date)"
```
*   `echo`: Prints to the screen.
*   `$(date)`: This is **command substitution**. It runs the `date` command and inserts its output into the string.

---

### **2. Variables**

Variables are crucial for storing data.

```bash
#!/bin/bash

# Define a variable (NO spaces around the = sign!)
NAME="John Doe"
COUNT=5

# Use the variable with $
echo "Hello, $NAME"
echo "Hello, ${NAME}" # Curly braces are optional but good for clarity.

# Use command output as a variable
CURRENT_DIR=$(pwd)
SERVER_NAME=$(hostname)
echo "You are in $CURRENT_DIR on server $SERVER_NAME"

# Arrays
SERVERS=("web01" "web02" "db01")
echo "First server: ${SERVERS[0]}"
echo "All servers: ${SERVERS[@]}"
```

---

### **3. Input & Arguments**

Scripts often need input from users or other scripts.

#### **a) Command-Line Arguments**
```bash
#!/bin/bash
# ./script.sh arg1 arg2 arg3

echo "Script Name: $0"
echo "First Argument: $1"
echo "Second Argument: $2"
echo "All Arguments: $@"
echo "Number of Arguments: $#"
```

#### **b) Reading Input from User**
```bash
#!/bin/bash
echo "What is your name?"
read NAME # Input stored in $NAME
echo "Hello, $NAME"

# Silent read for passwords
read -s -p "Enter your password: " PASSWORD
echo
echo "Password received."
```

---

### **4. Control Flow: The Real-World Essentials**

#### **a) If / Else Statements**
Used for making decisions based on conditions (like checking if a file exists, if a command succeeded, etc.).

**Syntax:**
```bash
if [ condition ]; then
  # commands
elif [ another_condition ]; then
  # other commands
else
  # default commands
fi
```

**Common Test Conditions (`[ ]`):**
*   **File Checks:**
    *   `-f "/path/to/file"` : True if file **exists**.
    *   `-d "/path/to/dir"` : True if directory exists.
*   **String Comparisons:**
    *   `"$var" = "value"` : True if equal.
    *   `-z "$var"` : True if string is empty.
*   **Number Comparisons:**
    *   `$num -eq 5` : Equal
    *   `$num -gt 10` : Greater Than
*   **Command Exit Status:**
    *   `if command; then...` : The `if` checks if the command succeeded (exit code 0).

**Real-World Example:**
```bash
#!/bin/bash

# Check if a file exists before backing it up
CONFIG_FILE="/etc/app/config.conf"
BACKUP_DIR="/backup"

if [ ! -f "$CONFIG_FILE" ]; then
  echo "ERROR: Config file $CONFIG_FILE missing!"
  exit 1 # Exit the script with an error code
fi

if [ ! -d "$BACKUP_DIR" ]; then
  echo "Creating backup directory..."
  mkdir -p "$BACKUP_DIR"
fi

echo "Backing up config file..."
cp "$CONFIG_FILE" "$BACKUP_DIR/config.conf.bak"

# Check if the cp command was successful
if [ $? -eq 0 ]; then
  echo "Backup completed successfully."
else
  echo "Backup failed!" >&2 # Print to stderr
  exit 1
fi
```

#### **b) For Loops**
Used to iterate over a list (e.g., servers, files).

**Iterating over a list:**
```bash
#!/bin/bash
# Loop through a list of servers
for SERVER in "web01" "web02" "db01"; do
  echo "Deploying to $SERVER"
  # You would run your deployment commands here, like scp/ssh
  # scp package.tar.gz user@$SERVER:/tmp/
done
```

**Iterating over command output:**
```bash
#!/bin/bash
# Loop through all .conf files in /etc
for CONFIG in /etc/*.conf; do
  echo "Found config file: $CONFIG"
  # Process each file
done
```

---

### **5. Functions**
Organize your code into reusable blocks. This is critical for clean, maintainable scripts.

```bash
#!/bin/bash

# Define a function
log_message() {
  local MESSAGE="$1" # 'local' keyword limits variable scope to the function
  echo "[$(date)]: $MESSAGE"
}

# Define a function with parameters
check_disk_usage() {
  local THRESHOLD=$1
  local USAGE=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

  if [ "$USAGE" -gt "$THRESHOLD" ]; then
    log_message "CRITICAL: Disk usage is $USAGE%"
    return 1 # Return a non-zero exit code for failure
  else
    log_message "OK: Disk usage is $USAGE%"
    return 0
  fi
}

# Call the functions
log_message "Starting health check..."
check_disk_usage 90 # Check if usage is above 90%

# Capture the return code of the function
if [ $? -eq 0 ]; then
  log_message "Health check passed."
else
  log_message "Health check failed. Sending alert..." >&2
  # Add alerting logic here (e.g., send an email, Slack message)
fi
```

---

### **6. Error Handling: Making Scripts Robust**
This is what separates amateur scripts from professional ones.

#### **a) Exit on Error (`set -e`)**
The most important line in your script. It makes the script exit immediately if any command fails.
```bash
#!/bin/bash
set -e # Exit on first error
# Also very common to use:
set -o pipefail # Causes a pipeline to fail if any command in it fails
```

#### **b) Error Logging**
Always log errors to a file and to stderr.
```bash
#!/bin/bash
LOG_FILE="/var/log/my_script.log"

exec > >(tee -a "$LOG_FILE") 2>&1 # Redirect all script output to the log file AND the terminal

echo "Script started..."
# Your commands here
```

#### **c) Traps (Cleanup on Exit)**
Run commands when the script exits (e.g., to clean up temp files, even on a crash).
```bash
#!/bin/bash
set -e

TEMP_FILE="/tmp/mytemp$$" # $$ is the script's PID, good for unique names

cleanup() {
  echo "Cleaning up temp files..."
  rm -f "$TEMP_FILE"
  echo "Cleanup complete."
}

# Register the cleanup function to run on EXIT signal
trap cleanup EXIT

# Script logic...
echo "Creating temp file..."
touch "$TEMP_FILE"
# ... do work with the temp file ...
# The cleanup function will be called automatically when the script ends.
```

---

### **Putting It All Together: A Real-World Example**

**Scenario:** A script to check the health of multiple web servers and send an alert if any are down.

```bash
#!/bin/bash
set -euo pipefail # Exit on error, undefined variable, and pipe failure

# Configuration
SERVERS=("https://web01.example.com" "https://web02.example.com")
ALERT_EMAIL="admin@example.com"
LOG_FILE="/var/log/server_health.log"

# Function to log messages with timestamp
log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Function to send an alert (placeholder for real alerting tool like Mailgun, PagerDuty)
send_alert() {
  local SERVER=$1
  local STATUS=$2
  log "ALERT: Server $SERVER is down! Status: $STATUS"
  # Example using mail command:
  # echo "Server $SERVER is down. Status code: $STATUS" | mail -s "SERVER DOWN" "$ALERT_EMAIL"
}

# Function to check a single server
check_server() {
  local URL=$1
  local RESPONSE_CODE
  # Use curl to get the HTTP response code, with a timeout
  RESPONSE_CODE=$(curl -s -o /dev/null -w "%{http_code}" --max-time 5 "$URL" || echo "000")
  if [ "$RESPONSE_CODE" -ge 200 ] && [ "$RESPONSE_CODE" -lt 400 ]; then
    log "OK: $URL responded with $RESPONSE_CODE"
    return 0
  else
    send_alert "$URL" "$RESPONSE_CODE"
    return 1
  fi
}

log "Starting server health check..."

# Main loop: check all servers
for SERVER in "${SERVERS[@]}"; do
  if ! check_server "$SERVER"; then
    # The error is already logged by check_server and send_alert
    # We could add a global FAILED flag here
    :
  fi
done

log "Health check cycle complete."
```

### **Next Steps & Best Practices**
1.  **Use ShellCheck:** Always lint your scripts with `shellcheck script.sh`. It's a fantastic tool that finds bugs and suggests improvements.
2.  **Use Configuration Files:** Don't hardcode values like server lists. Source them from a separate config file (e.g., `source config.cfg`).
3.  **Use Version Control:** Store all your scripts in Git.
4.  **Know When to Stop:** Shell scripts are great for gluing commands. If you need complex data structures or heavy logic, switch to a more powerful language like Python.