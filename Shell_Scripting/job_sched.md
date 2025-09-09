# Job Scheduling using Cron

Cron is a system daemon used to execute tasks in the background at designated times.

A **crontab** file is a simple text file containing a list of commands to be run at specified times. It is edited using the `crontab` command.

Each line in a crontab file has five time-and-date fields, followed by a command. The fields are separated by spaces.

## Crontab Time and Date Fields

| Field   | Allowed Values         | Description         |
|---------|-----------------------|---------------------|
| minute  | 0-59                  | Minute of the hour  |
| hour    | 0-23 (0 = midnight)   | Hour of the day     |
| day     | 1-31                  | Day of the month    |
| month   | 1-12                  | Month of the year   |
| weekday | 0-6 (0 = Sunday)      | Day of the week     |

## Common Crontab Commands

- List current crontab:
    ```sh
    crontab -l
    ```

- Edit crontab:
    ```sh
    crontab -e
    ```

## Example: Adding a Cron Job

To run a command every day at 9:00 p.m. and append output to `/tmp/echo.txt`:

```sh
0 21 * * * echo "Welcome to cron" >> /tmp/echo.txt
```

- `minute` = 0
- `hour` = 21 (9 p.m.)
- Runs every day, every month, every weekday.

Check if the job is added:

```sh
crontab -l
```

Example output:
```
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
0 21 * * * echo "Welcome to cron" >> /tmp/echo.txt
```

---

## Schedule a Shell Script

**Step 1:** Create a new file named `diskusage.sh`.

**Step 2:** Add the following content:

```bash
#! /bin/bash
# print the current date time
date
# print the disk free statistics
df -h
```

**Step 3:** Save the file.

**Step 4:** Make the script executable and test it:

```sh
chmod u+x diskusage.sh
./diskusage.sh
```

Sample output:
```
Mon Sep  8 22:32:31 EDT 2025
Filesystem      Size  Used Avail Use% Mounted on
overlay          98G   56G   38G  60% /
...
```

**Step 5:** Schedule the script to run every day at midnight and append output to `/home/project/diskusage.log`:

Edit the crontab:

```sh
crontab -e
```

Add this line:

```sh
0 0 * * * /home/project/diskusage.sh >> /home/project/diskusage.log
```

Check the crontab:

```sh
crontab -l
```

---

## Remove the Crontab

To remove the current crontab:

```sh
crontab -r
```

Verify removal:

```sh
crontab -l
```

Output will be:

```
no crontab for theia
```
