# Linux Cronjobs

`cron` is a time-based job scheduler in Unix-like systems (Linux, macOS). It runs **cronjobs** (commands or scripts scheduled to run at specific times or intervals automatically) and it runs as a **background daemon** called `crond`.

It is for "simple" tasks. For advanced/complex tasks use "Shell Scripting".

They are defined in `crontabs`.
1. **System-wide crontabs**
    * `/etc/crontab` — traditional system cron table
    * `/etc/cron.d/` — directory for package/app-specific cron tables
    * `/etc/cron.daily/`, `/etc/cron.hourly/`, `/etc/cron.weekly/`, `/etc/cron.monthly/` — drop-in folders for scripts
2. **User crontabs** --> Each user can have their own crontab. Managed with the `crontab` command.

```bash
crontab -e    # Edit current user's crontab
crontab -l    # List your cronjobs
crontab -r    # Remove your crontab
```

## Cron Syntax
It is defined in a continuous line. Always starting off with the "schedule" which is defined by `* * * * *`.

```
* * * * * command_to_run
| | | | |
| | | | +----- day of week (0 - 7) (Sunday = 0 or 7)
| | | +------- month (1 - 12)
| | +--------- day of month (1 - 31)
| +----------- hour (0 - 23)
+------------- minute (0 - 59)
```

1. `0 0 * * *` - Every day at midnight.
2. `0 */2 * * *` - Every 2 hours.
3. `*/5 * * * *` - Every 5 minutes.
4. `0 9-17 * * 1-5` - Every hour from 9am to 5pm, Mon–Fri.
5. `0 0 1 * *` - On the first day of every month.

> Example: `30 5 * * * /home/user/script.sh` - Runs script.sh every day at **05:30 AM**.

There are also @ shortcuts: `@hourly`, `@daily`, `@weekly`, `@monthly`, `@yearly` and `@reboot`.

## Environment
The default shell is `/bin/sh`. You can override:

  ```crontab
  SHELL=/bin/bash
  ```

The default `PATH` is very minimal — use full paths or define your own:

  ```crontab
  PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
  ```

## Output & logging
By default, cron emails the output to the user (if `mail` is set up).

```bash
# Discard all output
* * * * * /path/to/command > /dev/null 2>&1

# Log to file
* * * * * /path/to/command >> /var/log/myjob.log 2>&1
```

## Examples

```cron
0 2 * * 1-5 /usr/bin/flock -n /tmp/backup.lock /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

* `0 2 * * 1-5` → Runs **at 2:00 AM**, **Monday to Friday**.
* `/usr/bin/flock -n /tmp/backup.lock` → Uses `flock` to prevent multiple instances from running simultaneously.
* `/usr/local/bin/backup.sh` → Runs a custom backup script.
* `>> /var/log/backup.log 2>&1` → Appends both stdout and stderr to a log file for troubleshooting.

```cron
15 3 1 * * /usr/bin/find /var/log/myapp/ -name "*.log" -mtime +30 -delete && /usr/bin/find /var/log/myapp/ -name "*.log" -exec gzip {} \; >> /var/log/myapp/maintenance.log 2>&1
```

* `15 3 1 * *` → Runs **at 3:15 AM on the 1st day of every month**.
* `find /var/log/myapp/ -name "*.log" -mtime +30 -delete`
  → Deletes all `.log` files older than **30 days**.
* `&&` → Only if deletion succeeds, do the next command.
* `find /var/log/myapp/ -name "*.log" -exec gzip {} \;`
  → Compresses all current `.log` files with `gzip`.
* `>> /var/log/myapp/maintenance.log 2>&1`
  → Appends all output (and errors) to a `maintenance.log`.


