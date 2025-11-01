## Investigate Application Logs

### Task 1
Filter the **~/project/logs/app.log** file to find all lines containing the word ERROR.
### Solution
```bash
grep -w "ERROR" app.log
```

### Task 2
- Examine the system's kernel messages for any lines related to *fail or error*.
- Save these findings into a file named **~/project/boot_issues.txt**.
### Requirements
- You must use the dmesg command to view kernel messages.
- Your search for fail or error should be case-insensitive.
- The results must be saved to a file named ~/project/boot_issues.txt.
- Note: You may need administrative privileges (sudo) to access kernel messages.
### Hints
- The **dmesg** command displays kernel messages. You can "pipe" its output to another command for filtering.
- The **pipe operator |** sends the output of one command to the input of another.
- The grep command's **-i** option makes the search case-insensitive.
- To search for multiple patterns at once (like fail OR error), you can use 
```bash grep -E 'pattern1|pattern2'```
- Note: If you encounter a "Operation not permitted" error, try running the command with sudo to gain the necessary privileges.
### Solution
```bash
sudo dmesg | grep -iE 'fail|error' > ~/project/boot_issues.txt
```

### Task 3
- Search the web server configuration file at **~/project/config/nginx.conf**.
- Find the line containing the **worker_processes** directive.
- Append this line to the **~/project/error_report.txt** file you created in the first step.
### Requirements
- The input file is **~/project/config/nginx.conf**.
- You must append the result to **~/project/error_report.txt**, not overwrite it.
### Hints
- You can use **grep** again for this task.
- To **append** output to a file instead of overwriting it, **use the >> operator**.
### Solution
```bash
grep -w "worker_processes" ~/project/config/nginx.conf >> ~/project/error_report.txt"
```

### Task 4
- Compare the staging configuration file **~/project/config/staging/app.conf** with the production configuration file **~/project/config/production/app.conf**.
- Save the differences to a new file named **~/project/config_diff.txt**.
### Requirements
- You must use the **diff** command.
- The output showing the differences must be saved to **~/project/config_diff.txt**.
### Hints
- The **diff** command is designed specifically for *comparing two files line by line.*
- The basic syntax is ```diff file1 file2```, where it shows what changes need to be made to file1 to make it identical to file2.
- The order of files matters! ```diff A B``` and ```diff B A``` will show different outputs.
- You can redirect the output of diff to a file just like you did with grep.
### Solution
```bash
diff ~/project/config/staging/app.conf ~/project/config/production/app.conf >> ~/project/config_diff.txt
```

### Task 4
- You have two directories: **/home/labex/project/server1_files** (representing the staging server) and **/home/labex/project/server2_files** (representing the production server).
- Compare these two directories to find out which files are *unique to server1_files.*
- Save the complete comparison output to a file named **/home/labex/project/missing_files.txt.**
### Requirements
- You must use the **diff** command to compare the two directories.
- The output must be saved to **/home/labex/project/missing_files.txt**.
### Hints
- The **diff** command can also compare directories if you provide directory paths instead of file paths.
- Using the **-r** or **--recursive** option with diff is a good practice for comparing directories, as it will compare all files within them.
- The output format of diff on directories will explicitly state which files are **"Only in"** a specific directory.
- Just like with files, the order matters when comparing directories. ```diff dir1 dir2``` shows what's in dir1 but not in dir2, while ```diff dir2 dir1``` shows the opposite.
### Solution
```bash
diff -r home/labex/project/server1_files /home/labex/project/server2_files > /home/labex/project/missing_files.txt
```

### Summary
- **grep:** To filter log files and extract critical error information.
- **dmesg:** To investigate system-level hardware and kernel issues.
- **diff:** To compare configuration files and identify discrepancies between environments.
- **Command pipelines and redirection:** To efficiently process and document your findings.