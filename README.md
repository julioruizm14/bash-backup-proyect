# **Bash Backup Project**

This Bash script is an automated backup tool designed to identify, compress, and archive **only files that have been modified or created in the last 24 hours** within a specific directory.

It is ideal for performing daily backups of work-in-progress without duplicating old files that haven't changed.

## **🚀 Key Features**

* **Time Filtering:** Calculates the current date and selects only files with a modification date later than 24 hours ago (currentTS \- 24h).  
* **Input Validation:** Verifies that exactly two arguments are passed and that both are valid directories before execution.  
* **Unique Naming:** Generates backup files with the format backup-\[TIMESTAMP\].tar.gz (using Unix time) to prevent overwrites.  
* **Path Handling:** Manages absolute and relative paths internally to ensure the compressed file is moved to the correct destination regardless of where the script is executed from.

## **📋 Requirements**

* Unix/Linux/macOS environment.  
* **Bash** interpreter.  
* **tar** compression tool.

## **💻 Manual Usage**

The script requires two mandatory arguments: the source directory (what to check) and the destination directory (where to save the backup).

./backup.sh \<source\_directory\> \<destination\_directory\>

### **Example**

Suppose you have a projects folder and want to save today's changes into backups:

./backup.sh ./projects ./backups

## **🤖 Automation (CronJob)**

To make this script truly useful, you can configure it to run automatically every 24 hours using cron.

1. Move the script to a system folder (optional but recommended):  
   sudo cp backup.sh /usr/local/bin/

2. Open the scheduled tasks editor:  
   crontab \-e

3. Add the following line at the end of the file to run the backup **every day at midnight**:  
   0 0 \* \* \* /usr/local/bin/backup.sh /path/to/my\_data /path/to/my\_backups

*Note: Make sure to use absolute paths (e.g., /home/user/docs) for both the script and the folders in the cron command.*

## **⚙️ Technical Explanation**

The script performs the following logical steps:

1. **Validation:** Checks if NumArgs \== 2 and if the directories exist (-d).  
2. **Timestamps:** Gets the current time in Unix format (date \+%s) and calculates the threshold for 24 hours ago.  
3. **Path Resolution:** Saves the absolute path of the destination to correctly move the final file after changing directories with cd.  
4. **Selection Loop:**  
   * Enters the target directory.  
   * Iterates over each file (for file in \*).  
   * Compares the file's modification date with the $yesterdayTS variable.  
   * If it is recent, it adds it to the toBackup array.  
5. **Compression:** Executes tar only with the files listed in the toBackup array.

## **⚠️ Important Notes**

* If no files have been modified in the last 24 hours, the tar command might generate an error or create an empty archive (depending on the tar version), as the file array will be empty.  
* The script is not recursive by default in its selection (the for file in \* loop checks files in the root of the target directory, but tar will include folders if they were recently modified).
