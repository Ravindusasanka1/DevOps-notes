# Things learned from LINUX chapter and the labs in KodeKloud

---

````markdown
# 🐧 Linux Commands Cheat Sheet (Practice Notes)

## 📍 Show Current Directory
```bash
pwd
````

* Prints the **current working directory**.
* Example output:

  ```
  /home/thor
  ```

---

## 📂 View Directory Structure

```bash
tree /home/thor/test_dir
```

* Shows the **directory tree structure**.
* Example:

  ```
  /home/thor/test_dir
  ├── dir1
  ├── dir2
  ├── dir3
  ├── test_file1.txt
  ├── test_file2.txt
  └── test_file4.txt
  ```

---

## 📄 Create Files

```bash
touch /home/thor/empty_file.txt
```

* Creates an **empty file**.
* ⚠️ Common mistake: `touch/home/file.txt` (missing space after `touch`).

---

## 📁 Create Directories

```bash
mkdir /home/thor/empty_dir
```

* Creates a new directory.

```bash
mkdir -p /home/thor/asia/india/bangalore
```

* `-p` ensures **parent directories** are created if they don’t exist.

---

## 📑 Copy Files & Directories

```bash
cp -v /home/thor/asia/bangalore.txt /home/thor/asia/india/banglore
```

* Copies file to a directory (`banglore`).
* `-v` = verbose (shows what’s happening).

```bash
cp -v /home/thor/asia/bangalore.txt /home/thor/asia/india/bangalore
```

* Copies file **into a directory** with the same filename.

```bash
cp -r /home/thor/asia/india/bangalore /home/thor/
```

* `-r` = recursive. Copies entire **directory and its contents**.

⚠️ Common mistake:

* `cp-r` ❌ → should be `cp -r` ✅

---

## 🗑 Delete Files & Directories

```bash
rm /home/thor/asia/bangalore.txt
```

* Deletes a file.

```bash
rm -r /home/thor/asia/india/bangalore
```

* Deletes a directory **recursively**.

⚠️ Common mistake: Trying to delete a file with wrong spelling (`banglore.txt` vs `bangalore.txt`).

---

# ✅ Key Learnings

1. Always add a **space** between command and arguments (`touch file.txt`, not `touchfile.txt`).
2. Use `mkdir -p` to create **nested directories** in one step.
3. Use `cp -v` for clear confirmation while copying.
4. Add `-r` when copying or deleting **directories**.
5. Double-check **spelling and paths** (e.g., `banglore` vs `bangalore`).

---
## commands and arguments


---

# 🖥️ Commands and Arguments in Linux

## 🔹 Command

* A **command** is the program or utility you run in the shell.
* Example:

  ```bash
  ls
  ```

  → Here, `ls` is the command (it lists files in a directory).

---

## 🔹 Argument

* An **argument** is extra information you pass to the command to tell it *what to act on*.
* Example:

  ```bash
  ls /home/thor
  ```

  * `ls` → command
  * `/home/thor` → argument (the directory you want to list)

Without arguments, most commands run with **default behavior**. With arguments, you control what they do.

---

## 🔹 Options (Flags)

* Options (also called **flags**) are **special arguments** that usually start with `-` or `--`.
* They modify the *behavior* of the command.
* Example:

  ```bash
  ls -l /home/thor
  ```

  * `ls` → command
  * `-l` → option (long listing format)
  * `/home/thor` → argument (directory to list)

You can also combine options:

```bash
ls -la /home/thor
```

* `-l` = long listing
* `-a` = show hidden files
* Together: show all files in long format.

---

## 🔹 Full Breakdown Example

```bash
cp -r /home/thor/asia/india/bangalore /home/thor/
```

* `cp` → **command** (copy files/directories)
* `-r` → **option/flag** (recursive, needed to copy directories)
* `/home/thor/asia/india/bangalore` → **argument** (source path)
* `/home/thor/` → **argument** (destination path)

---

## ✅ Key Difference

* **Command** = what to do
* **Option** = how to do it (change behavior)
* **Argument** = what to act on (file/directory/input)

---

# **example.**

````markdown
# 🖥️ Commands, Options, and Arguments in Linux

## 🔹 Command
- The actual program or utility you run.
- Example:  
  ```bash
  ls
````

→ `ls` is the command.

---

## 🔹 Argument

* Extra information passed to the command, usually **file/directory names**.
* Example:

  ```bash
  ls /home/thor
  ```

  * `ls` → command
  * `/home/thor` → argument (directory to list)

---

## 🔹 Option (Flag)

* Modifies the **behavior** of the command.
* Usually starts with `-` or `--`.
* Example:

  ```bash
  ls -l /home/thor
  ```

  * `ls` → command
  * `-l` → option (long listing format)
  * `/home/thor` → argument

Options can be combined:

```bash
ls -la /home/thor
```

* `-l` = long format
* `-a` = show hidden files

---

## 🔹 Full Example

```bash
cp -r /home/thor/asia/india/bangalore /home/thor/
```

* `cp` → **command** (copy)
* `-r` → **option** (recursive)
* `/home/thor/asia/india/bangalore` → **argument** (source)
* `/home/thor/` → **argument** (destination)

---

## 📊 Quick Reference Table

| Part         | Meaning                                | Example                    |
| ------------ | -------------------------------------- | -------------------------- |
| **Command**  | The action/program to run              | `ls`, `cp`, `mkdir`        |
| **Option**   | Modifies how the command works         | `-l`, `-r`, `-p`, `--help` |
| **Argument** | Target of the command (what to act on) | `/home/thor`, `file.txt`   |

---

## ✅ Key Points

1. **Command** = What you want to do
2. **Option** = How you want to do it (change behavior)
3. **Argument** = What you want to act on

# **vi editor**
vi,vim there are more
Absolutely! This cover **features, modes, and basic usage** so it’s practical for exams and practice.

---

````markdown
# 📝 Vi/Vim Editor Notes

## 🔹 What is Vi?
- `vi` (or `vim`) is a **text editor** in Linux, available in almost all distributions.  
- Lightweight, fast, and works in terminal.  
- Used for editing configuration files, code, scripts, and notes.

---

## 🔹 Features of Vi/Vim
1. **Modes** – Two main modes:
   - **Normal Mode**: For navigation and commands.
   - **Insert Mode**: For typing/editing text.
   - **Visual Mode**: For selecting text.
   - **Command-Line Mode**: For saving, quitting, searching, etc.
2. **Undo/Redo** support.
3. **Search & Replace** within files.
4. **Copy, Cut, Paste** using commands.
5. **Syntax highlighting** (in vim).
6. Works entirely in **terminal**, so no GUI required.

---

## 🔹 How to Open a File
```bash
vi filename.txt
````

* Opens `filename.txt` in vi editor.
* If file doesn’t exist, it creates a new file.

---

## 🔹 Vi Modes

| Mode         | Purpose                     | How to Enter                                               |
| ------------ | --------------------------- | ---------------------------------------------------------- |
| Normal       | Navigate and run commands   | Default mode on opening vi                                 |
| Insert       | Type text                   | `i` (insert at cursor), `a` (after cursor), `o` (new line) |
| Visual       | Select text                 | `v` (character-wise), `V` (line-wise)                      |
| Command-line | Save, quit, search, replace | `:` (colon)                                                |

---

## 🔹 Basic Commands

### In **Normal Mode**:

* `h` → Move left
* `j` → Move down
* `k` → Move up
* `l` → Move right
* `dd` → Delete current line
* `yy` → Copy (yank) current line
* `p` → Paste after cursor
* `u` → Undo last change
* `Ctrl + r` → Redo

### Enter **Insert Mode**:

* `i` → Insert before cursor
* `a` → Insert after cursor
* `o` → Open new line below current line
* Press `Esc` to return to **Normal Mode**

### Command-Line Mode (`:`):

* `:w` → Save file
* `:q` → Quit vi
* `:wq` → Save and quit
* `:q!` → Quit without saving
* `:set number` → Show line numbers
* `:/pattern` → Search for a pattern
* `:%s/old/new/g` → Replace all occurrences of "old" with "new"

---

## 🔹 Tips & Tricks

1. Always press **Esc** to ensure you are in Normal Mode before running commands.
2. Combine commands:

   * `3dd` → Delete 3 lines
   * `5yy` → Copy 5 lines
3. To quickly navigate:

   * `gg` → Go to first line
   * `G` → Go to last line
   * `:n` → Go to line number n

---

## ✅ Key Points

* Vi is **mode-based**, so know which mode you are in.
* **Esc** always brings you back to Normal Mode.
* Commands are often **shortcuts**, not menus.
* Great for editing text in servers without GUI.

---
## VI editor lab
in the editor 
>yy - copy a line

>dd- delete

>p - paste

>x - delete a single character

# **more linux commands**
 Linux user accounts and `sudo` — this is foundational for DevOps. 
---

# Linux Users & `sudo` Cheatsheet

````markdown
# Linux Users & sudo

## 1. User Accounts

### Check current user
```bash
whoami
````

### List all users

```bash
cat /etc/passwd
```

* Each line = a user account
* Format: username\:x:UID\:GID\:comment\:home\:shell

### Add a new user

```bash
sudo adduser john
```

* Creates home directory `/home/john`
* Prompts for password and details

### Delete a user

```bash
sudo deluser john
```

### Change user password

```bash
sudo passwd john
```

### Switch user

```bash
su - john   # switch to user john
```

---

## 2. Groups

### List groups

```bash
groups
```

### Add user to a group

```bash
sudo usermod -aG docker john
```

* `-aG` = append user to group without removing other groups

---

## 3. sudo (Superuser Do)

### Check if user can use sudo

```bash
sudo -l
```

### Run commands as root

```bash
sudo apt update
sudo systemctl restart nginx
```

### Edit sudoers safely

```bash
sudo visudo
```

* Never edit `/etc/sudoers` directly
* Gives specific permissions to users or groups

### Add user to sudo group

```bash
sudo usermod -aG sudo john
```

* Now `john` can run `sudo` commands

---

## 4. Useful Notes / Tips

* Only give `sudo` to trusted users.
* `su -` switches to root temporarily (requires root password).
* Always use `sudo` for admin tasks instead of logging in as root.
* Check file permissions with `ls -l` to see which users/groups can access files.
## More linux commands in labs
Absolutely! I’ve converted all your recent terminal commands and lessons into a **Markdown-ready DevOps note** that you can drop into your `lab01.md` or `cheatsheet.md` file.

---

````markdown
# Lab  – Basic Linux Commands, Users, sudo & Downloading Files

## 1. Current User & Groups

### Check current user
```bash
whoami
````

Output:

```
thor
```

### Check user ID, group ID, and groups

```bash
id
```

Output:

```
uid=1001(thor) gid=1001(thor) groups=1001(thor),10(wheel)
```

---

## 2. Listing Files in Root Directory

### Correct usage

> **Note:** Do not type "Run" — it’s just instruction text.

```bash
sudo ls /root/
```

* Lists all files in `/root` directory, e.g., `anaconda-ks.cfg`.

---

## 3. Downloading Files Using curl

### Common mistakes

```bash
curl -0 <URL>          # ❌ invalid option (-0)
curl -O<URL><URL>      # ❌ wrong syntax
```

### Correct usage

```bash
curl -O https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf
```

* `-O` downloads the file and saves it with the **same name** as on the server (`dummy.pdf`).

---

## 4. Checking OS Version

### Do NOT type the path directly as a command

```bash
/etc/*release*   # ❌
```

### Correct usage

```bash
cat /etc/centos-release
```

Output:

```
CentOS Stream release 9
```

Or for detailed info:

```bash
cat /etc/os-release
```

---

## 5. Key Takeaways / Lessons Learned

* Instructions like “Run sudo ls /root/” should be typed **without the word "Run"**.
* Always check `curl` syntax. Use `-O` to download with the original filename.
* `sudo` is required to access restricted directories like `/root`.
* Use `cat` to read system files like `/etc/centos-release`.
* Mistakes are normal — observing errors helps you learn correct command usage.
---
# **Package Management**

````markdown
# 🐧 Linux Package Management Notes

## 🔹 What is Package Management?
- **Package managers**  are tools that install, update, remove, and manage software on Linux systems.  
- They handle **dependencies automatically**.  
- Common types:  
  - RPM-based systems (Red Hat, CentOS, Fedora) → `yum`, `dnf`, `rpm`  
  - Debian-based systems (Ubuntu, Debian) → `apt`, `dpkg`  
  - Arch-based systems → `pacman`  

---

## 🔹 YUM (Yellowdog Updater, Modified)
- Used in **RHEL, CentOS, Fedora** (older versions; newer Fedora uses `dnf`).  
- Manages **RPM packages**.

### Basic YUM Commands
```bash
# Install a package
yum install package_name

# Update a package
yum update package_name

# Update all packages
yum update

# Remove a package
yum remove package_name

# Search for a package
yum search package_name

# Show package info
yum info package_name

# List installed packages
yum list installed
````

---

## 🔹 DNF (Dandified YUM)

* Replacement for YUM in newer Fedora / RHEL versions.
* Faster, better dependency handling.

### Basic DNF Commands

```bash
# Install a package
dnf install package_name

# Remove a package
dnf remove package_name

# Update system
dnf update

# Search for package
dnf search package_name

# List installed packages
dnf list installed
```

---

## 🔹 RPM (Red Hat Package Manager)

* Lower-level package manager for **RPM packages**.
* Does **not resolve dependencies automatically**, unlike YUM/DNF.

### Basic RPM Commands

```bash
# Install a package
rpm -ivh package.rpm

# Upgrade a package
rpm -Uvh package.rpm

# Remove a package
rpm -e package_name

# Query package info
rpm -qi package_name

# List files in a package
rpm -ql package_name
```

* Options:

  * `-i` → install
  * `-U` → upgrade
  * `-v` → verbose
  * `-h` → show hash progress

---

## 🔹 Apt (Debian/Ubuntu)

* For Debian-based systems. Handles `.deb` packages.
* Example commands:

```bash
# Update repository cache
sudo apt update

# Upgrade all packages
sudo apt upgrade

# Install a package
sudo apt install package_name

# Remove a package
sudo apt remove package_name

# Search for a package
apt search package_name
```

---

## 🔹 Key Notes / Tips

* **Always update the repository cache** before installing (`yum update` or `apt update`).
* **Use sudo** if you are not root.
* **YUM/DNF** automatically resolve dependencies; **RPM** does not.
* To check package version:

```bash
yum info package_name
apt show package_name
rpm -qi package_name
```

* Clean cache to free space:

```bash
yum clean all
dnf clean all
apt clean
```

---

## 🔹 Quick Reference Table

| Package Manager | System Type   | Install          | Remove         | Update           | Search            |
| --------------- | ------------- | ---------------- | -------------- | ---------------- | ----------------- |
| yum             | RHEL/CentOS   | yum install pkg  | yum remove pkg | yum update       | yum search pkg    |
| dnf             | Fedora/RHEL   | dnf install pkg  | dnf remove pkg | dnf update       | dnf search pkg    |
| rpm             | RHEL/CentOS   | rpm -ivh pkg.rpm | rpm -e pkg     | rpm -Uvh pkg.rpm | rpm -qa / rpm -qi |
| apt             | Debian/Ubuntu | apt install pkg  | apt remove pkg | apt upgrade      | apt search pkg    |
 ## Package Management lab


````markdown
# Linux Package Management lab Notes

## Package Management Tools
- **RPM (Red Hat Package Manager)**: Low-level package management tool.
  - Installs, removes, queries, and verifies `.rpm` files.
  - Does **not resolve dependencies automatically**.
- **YUM / DNF (Yellowdog Updater, Modified / Dandified Yum)**: High-level package manager.
  - Installs packages from repositories.
  - Automatically resolves and installs dependencies.
  - Can remove packages and their unused dependencies.

---

## RPM Commands
- **Query packages**
```bash
rpm -q <package1> <package2> ...
````

Example:

```bash
rpm -q openssh-server python3 ansible telnet
```

* **Install a local RPM package**

```bash
sudo rpm -i /path/to/package.rpm
```

* **Remove a package**

```bash
sudo rpm -e <package_name>
```

* **Notes**

  * Installing via `rpm` does not fetch dependencies automatically.
  * Removal only removes the specified package, not dependencies installed manually.

---

## YUM Commands

* **Install package (with dependencies)**

```bash
sudo yum install -y <package_name>
```

Example:

```bash
sudo yum install -y maven
```

* **List installed package**

```bash
sudo yum list <package_name>
```

* **Remove package (and unused dependencies)**

```bash
sudo yum remove -y <package_name>
```

* **Key Points**

  * Downloads packages from repositories defined in `/etc/yum.repos.d/`.
  * Automatically resolves dependencies.
  * Can remove packages along with orphaned dependencies.

---

## Example Workflow from Session

1. Checked installed packages:

```bash
rpm -q openssh-server python3 ansible telnet
```

2. Tried installing a local RPM:

```bash
sudo rpm -i /opt/ftp-0.17-89.el9.x86_64.rpm
```

3. Removed it:

```bash
sudo rpm -e ftp-0.17-89.el9.x86_64
```

4. Installed `maven` via `yum` (with automatic dependencies):

```bash
sudo yum install -y maven
```

5. Verified installation:

```bash
sudo yum list maven
```

6. Removed `maven` and unused dependencies:

```bash
sudo yum remove -y maven
```

---

## Summary

* **RPM** → low-level, manual dependency management.
* **YUM/DNF** → high-level, resolves dependencies automatically.
* Prefer `yum`/`dnf` for installing from repos, `rpm` for local `.rpm` files.
# **Services**
Here’s a neat set of **Markdown notes** about Linux services and `systemctl`. You can copy-paste this into VS Code.

---

# 🖥️ Linux Services with `systemctl`

## 🔹 What is `systemctl`?

* `systemctl` is the main command to **manage systemd services**.
* It can **start, stop, restart, enable, disable, and check the status** of services.
* It replaces older tools like `service` and `chkconfig`.

---

## 🔹 Service Management Commands

### ▶️ Start a service

```bash
sudo systemctl start service_name
```

### ⏹️ Stop a service

```bash
sudo systemctl stop service_name
```

### 🔄 Restart a service

```bash
sudo systemctl restart service_name
```

### ✅ Enable service at boot

```bash
sudo systemctl enable service_name
```

### ❌ Disable service at boot

```bash
sudo systemctl disable service_name
```

### 📌 Check status

```bash
sudo systemctl status service_name
```

### 🔍 Check if service is enabled

```bash
systemctl is-enabled service_name
```

### 🔍 Check if service is active

```bash
systemctl is-active service_name
```

---

## 🔹 Target Management (Runlevels Replacement)

* `systemctl` manages **targets** (like runlevels).

```bash
systemctl get-default        # Show default target
sudo systemctl set-default multi-user.target   # Set runlevel 3 (CLI mode)
sudo systemctl set-default graphical.target    # Set runlevel 5 (GUI mode)
```

---

## 🔹 Useful Examples

* Restart **Apache web server**:

```bash
sudo systemctl restart httpd
```

* Start and enable **MySQL**:

```bash
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

* Disable and stop **firewalld**:

```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

---

## 📝 Key Learnings

* `systemctl` is the **go-to tool** for managing services.
* Replaces old `service` and `chkconfig`.
* Can also manage **system states (targets)** like reboot, poweroff, rescue, etc.
* Important for **troubleshooting, automation, and server admin tasks**.

---
## Services lab notes


````markdown
# ⚙️ Linux Services with `systemctl`

## 🔹 What is `systemctl`?
- Command-line tool to interact with **systemd**, the init system.
- Manages **services, targets, jobs, system states, and environment**.
- Replaces older tools (`service`, `chkconfig`).

---

## 🔹 Types of Commands

### 1. **Unit Commands**
- Units = services, sockets, mounts, timers, etc.
```bash
systemctl list-units                 # List active units
systemctl list-timers                # List timer units
systemctl status <unit>              # Show status of a service/unit
systemctl start <unit>               # Start service
systemctl stop <unit>                # Stop service
systemctl restart <unit>             # Restart service
systemctl reload <unit>              # Reload config without stopping
systemctl is-active <unit>           # Check if running
systemctl is-failed <unit>           # Check if failed
systemctl list-dependencies <unit>   # Show dependencies
systemctl cat <unit>                 # View unit file content
````

---

### 2. **Unit File Commands**

* Unit files describe how services behave (`/usr/lib/systemd/system/*.service`).

```bash
systemctl list-unit-files            # Show all installed service files
systemctl enable <unit>              # Enable service at boot
systemctl disable <unit>             # Disable service at boot
systemctl mask <unit>                # Prevent starting (even manually)
systemctl unmask <unit>              # Re-enable masked service
systemctl edit <unit>                # Edit override file
systemctl revert <unit>              # Revert to vendor defaults
systemctl set-default graphical.target   # Boot into GUI by default
systemctl get-default                    # Show default target
```

---

### 3. **System Commands**

* Control overall system state:

```bash
systemctl reboot             # Reboot system
systemctl poweroff           # Shutdown system
systemctl halt               # Halt system
systemctl rescue             # Enter rescue mode (single-user mode)
systemctl emergency          # Emergency shell
systemctl is-system-running  # Check overall system state
```

---

### 4. **Manager Commands**

* Affect the systemd process itself:

```bash
systemctl daemon-reload      # Reload unit files (after editing)
systemctl daemon-reexec      # Restart systemd manager
```

---

### 5. **Job & Environment Commands**

```bash
systemctl list-jobs                  # List pending jobs
systemctl cancel <job_id>            # Cancel a job
systemctl show-environment           # Show environment variables
systemctl set-environment VAR=VALUE  # Set env var
systemctl unset-environment VAR      # Unset env var
```

---

## 🔹 Practical Example: Apache (httpd)

```bash
# Check status
sudo systemctl status httpd

# Start service
sudo systemctl start httpd

# Enable at boot
sudo systemctl enable httpd

# Stop service
sudo systemctl stop httpd

# Disable at boot
sudo systemctl disable httpd
```

---

## 🔹 Custom Service Example

Unit file at: `/usr/lib/systemd/system/app.service`

```ini
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
ExecStartPre=/bin/bash /opt/code/configure_db.sh
ExecStartPost=/bin/bash /opt/code/email_status.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

Usage:

```bash
# View service file
systemctl cat app.service

# Start and enable
sudo systemctl start app
sudo systemctl enable app
```

---

## 📝 Key Learnings

* **Unit files** define how services behave (`/usr/lib/systemd/system/`).
* `systemctl start/stop/restart` → runtime control.
* `systemctl enable/disable` → boot-time control.
* `systemctl daemon-reload` required after editing/creating unit files.
* `systemctl cat <service>` → view effective config (including overrides).
* Targets replace SysV runlevels (`multi-user.target`, `graphical.target`).



 [DevOps Pre-Requisites PDF](./media/DevOps-Pre-Requisites-v2-1.pdf) look for this in the media folder








