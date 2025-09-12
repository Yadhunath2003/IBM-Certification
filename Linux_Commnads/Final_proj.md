# Shell Scripting Final Project

## Objective

Imagine that you are a lead Linux developer at the top-tech company ABC International Inc. ABC currently suffers from a huge bottleneck: each day, interns must painstakingly access encrypted password files on core servers and back up any files that were updated within the last 24 hours. This process introduces human error, lowers security, and takes an unreasonable amount of work.

As one of ABC Inc.'s most trusted Linux developers, you have been tasked with creating a script called `backup.sh` which runs every day and automatically backs up any encrypted password files that have been updated in the past 24 hours.

## Project

### Step 1: Download the `backup.sh` Script

Run the following command to download the `backup.sh` script:

```bash
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/pWN3kO2yWEuKMvYJdcLPQg/backup.sh
```

### Step 2: Script Details

The `backup.sh` script performs the backup procedure. Below is the script:

```sh 
#!/bin/bash

# This checks if the number of arguments is correct
# If the number of arguments is incorrect ( $# != 2) print error message and exit
if [[ $# != 2 ]]
then
    echo "backup.sh target_directory_name destination_directory_name"
    exit
fi

# This checks if argument 1 and argument 2 are valid directory paths
if [[ ! -d $1 ]] || [[ ! -d $2 ]]
then
    echo "Invalid directory path provided"
    exit
fi

# [TASK 1] -- setting the variable for target and destination directory.
targetDirectory="$1"
destinationDirectory="$2"

# [TASK 2] -- echo target and destination directories
echo "$targetDirectory"
echo "$destinationDirectory"

# [TASK 3] -- capture the current timestamp.
currentTS="$(date +%s)"

# [TASK 4] -- create a backup tar file and will be named based on the current timestamp.
backupFileName="backup-[${currentTS}].tar.gz"

# We're going to:
    # 1: Go into the target directory [TASK 5]
    # 2: Create the backup file [TASK 6]
    # 3: Move the backup file to the destination directory [TASK 7]

# To make things easier, we will define some useful variables...

# [TASK 5]
origAbsPath="$(pwd)"

# [TASK 6]
cd "$destinationDirectory" # <-
destDirAbsPath="$(pwd)"

# [TASK 7]
cd "$origAbsPath" # <-
cd "$targetDirectory" # <-

# [TASK 8] -- create a timestamp for the day before.
yesterdayTS=$(( currentTS - 24 * 60 * 60 ))

declare -a toBackup

for file in  * # [TASK 9] -- wildcard to iterate over all files and directories in the current folder.
do
    # [TASK 10] -- to check whether the $file was modified within the last 24 hours.
    if [[ "$(date -r "$file" +%s)" -gt "$yesterdayTS" ]]
    then
        # [TASK 11] -- add the $file that was updated in the past 24-hours to the toBackup array.
        toBackup+=("$file")
    fi
done

# [TASK 12] -- compress and archive the files.
tar -czvf "$backupFileName" "${toBackup[@]}"

# [TASK 13] -- move the back up file to the destination directory.
mv $backupFileName $destDirAbsPath/
echo "Backup saved to: $destDirAbsPath/$backupFileName"

# Congratulations! You completed the final project for this course!

```

### Step 3: Make the Script Executable

Save the `backup.sh` file and make it executable by running the following commands:

```bash
chmod u+x backup.sh
ls -l
```

### Step 4: Test the Script

1. Download the following `.zip` file using the `wget` command:

         ```bash
         wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-LX0117EN-SkillsNetwork/labs/Final%20Project/important-documents.zip
         ```

2. Unzip the archive file:

         ```bash
         unzip important-documents.zip
         ```

3. Update the file’s last-modified date to now:

         ```bash
         touch important-documents/*
         ```

4. Test the script using the following command:

         ```bash
         ./backup.sh important-documents .
         ```

         You should see a backup file with the current timestamp created. For example:

         ```bash
         /home/project$ ls -l
         total 24
         -rw-r--r-- 1 theia users 4423 Sep 12 14:31 'backup-[1757701888].tar.gz'
         -rwxr--r-- 1 theia users 1494 Sep 12 14:54  backup.sh
         drwxr-sr-x 2 theia users 4096 Sep 12 14:31  important-documents
         -rw-r--r-- 1 theia users 4995 Sep 28  2022  important-documents.zip
         ```

### Step 5: Automate the Script with Crontab

1. Copy the `backup.sh` script to the `/usr/local/bin` directory and make it executable:

         ```bash
         sudo cp ./backup.sh /usr/local/bin/backup.sh
         sudo chmod +x /usr/local/bin/backup.sh
         ```

2. Create a crontab to schedule the backup every 24 hours. Run the following command:

         ```bash
         crontab -e
         ```

         Add the following line to the crontab file:

         ```bash
         0 0 * * * /usr/local/bin/backup.sh /home/project/important-documents /home/project
         ```

3. Verify the crontab schedule:

         ```bash
         crontab -l
         ```

4. Start the crontab service:

         ```bash
         sudo service crontab start
         ```

         To stop the service, run:

         ```bash
         sudo service crontab stop
         ```

Congratulations! You have successfully completed the final project for Linux scripting basics.