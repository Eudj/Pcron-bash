# Pcron bash

The goal of this project is to recreate pcron using bash scripting.

# pcron - A Simple Task Scheduler

pcron is a lightweight task scheduler written in Bash. It allows users to schedule and automate tasks at specified intervals, similar to `cron`, but with a custom configuration system.

## Main goal

- **Scheduled execution**: Enable scheduled execution of user-defined tasks.
- **Access control**: Provide access control based on lists of authorizations (`allow`) and restrictions (`deny`).
- **Detailed logs**: Record executions and their results in a detailed log file.
- **Simple interface**: Provide a user-friendly interface for managing scheduled tasks.

## Installation
1. Clone the repository or download `pcron.sh`.
2. Move the main script to `/usr/bin/`:
   ```bash
	sudo mv pcron.sh /usr/bin/pcron.sh
	sudo chmod +x /usr/bin/pcron.sh
   ```
3. Create the configuration directory:
   ```bash
   sudo mkdir -p /etc/pcron/
   ```
4. Ensure logs are stored properly:
   ```bash
   sudo touch /var/log/pcron
   sudo chmod 666 /var/log/pcron
   ```

## Configuration
- Each user can have their own task file in `/etc/pcron/` named `pcrontab<user>` (e.g. `pcrontabalice`,`pcrontabbob`).
- The script for managing task files is located at `/etc/pcron/pcrontab`.
- Allowed users are listed in `/etc/pcron.allow`.
- Denied users are listed in `/etc/pcron.deny`.
- Execution logs are stored in `/var/log/pcron.log`, and execution states in `/var/log/state`.

## Usage
The `pcrontab<user>` files follow this pattern:
    ```bash
    * * * * * * <command>
    ```
each line containing a different schedule and command.

the rules for the time of execution are the following:

| sec | min | hour | day of month | month | day of the week | task |
| - | - | - | - | - | - | - |
| 0-3, 15 sec bracket | 0-59 | 0-23 | 1-31 | 1-12 | 1-7 | calling a bash script or a simple command |

### List
You can put `X:Y:Z` to form a list of values where the task will be scheduled
   ```bash
   * * 5:8:9 * * * echo "task on hour 5,8 and 9"
   ```
### Range
You can put `X-Y` to form a range of values where the task will be scheduled
   ```bash
   * 1-5 * * * * echo "task on minute 1,2,3,4 and 5"
   ```
### Reccurence
You can put `\X` to form a range reccurence
   ```bash
   * *\5 * * * * echo "task every 5 minutes"
   ```
### Exclusion
You can put `~X` to form an exclusion
   ```bash
   * *\5 *~5 * * * echo "task every 5 minutes, but not while it's 5AM"
   ```

## Commands
### Add a task for a user
   ```bash
   pcrontab -u <user> -e
   ```
This command opens the default editor (e.g., nano) to modify or add tasks in the user's configuration file. Example entry:
   ```bash
	* 1 * * * echo "Task executed by user"
   ```
This executes the command every hour on the first minute of the hour.

### List tasks for a user
   ```bash
	pcrontab -u <user> -l
   ```
Displays all scheduled tasks for the specified user.
	
### Remove a user's task file
   ```bash
	pcrontab -u <user> -r
   ```
Deletes the task file for the specified user, canceling all scheduled tasks.

### Manage user permissions
   ```bash
	pcrontab -a -u <user>  # Allow user to use pcron
	pcrontab -d -u <user>  # Deny user access to pcron
   ```
Adds the user to `/etc/pcron.allow` or `/etc/pcron.deny`, determining whether they can use pcron.

## Logging
All executed tasks are logged in `/var/log/pcron`, including timestamps and command outputs.
