## Create and Extract Compressed tar Archives

### Tasks
- Create **gzip** and **bzip2** compressed tar archives of the **/home** directory
- List the contents of both archives
- Extract both archives

### Requirements
- Perform all operations in the /home/labex directory
- Create the following archives:
    - Gzip-compressed: **/home/labex/home.tar.gz**
    - Bzip2-compressed: **/home/labex/home.tar.bz2**
- List the contents of both archives in **/home/labex**
- Extract both archives to **/home/labex/extracted**
- Use the tar command with appropriate options for all operations

### Solution
**tar:** The archiving utility.
```bash
sudo tar -czvf /home/labex/home.tar.gz /home
```
**-c:** Create a new archive.
**-z:** Compress the archive with gzip.
**-v:** Display verbose output, showing the files being added.
**-f:** Specify the filename of the archive.
**home_backup.tar.gz:** The name of the output archive file.
**/home:** The directory to be archived and compressed.

```bash
sudo tar -cjvf /home/labex/home.tar.bz2 /home
```
**-c:** Create a new archive.
**-j:** Compress the archive with bzip2.
**-v:** Display verbose output, showing the files being added.
**-f:** Specify the filename of the archive.
**home_backup.tar.bz2:** The name of the output archive file.
**/home:** The directory to be archived and compressed.

After creating the gzip-compressed archive, listing its contents might look like this:
```bash
tar -tvf /home/labex/home.tar.gz
```

Extract both archives to **/home/labex/extracted**
```bash
mkdir /home/labex/extracted
sudo tar -xvf /home/labex/home.tar.gz -C /home/labex/extracted
sudo tar -xvf /home/labex/home.tar.bz2 -C /home/labex/extracted
```
To extract files from a tar archive to a specific folder, use the **-C** option followed by the **target directory path**, as in ```tar -xf archive.tar -C /path/to/directory```. The target directory must exist beforehand, and you can create it using ```mkdir /path/to/directory```. 