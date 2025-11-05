## Create a Secure File for a New Project

### Tasks
- Create a new, empty file named project_keys.txt inside the **~/project/phoenix_project** directory.
- Set the permissions for this file so that only the owner has read and write access, and no one else (not even users in the same group) has any access.
### Requirements
- The file must be named **project_keys.txt**.
- The file must be located at **~/project/phoenix_project/project_keys.txt**.
- Use the **chmod** command with numeric notation to set the permissions.
### Hints
- You can create an empty file using the **touch** command.
- Remember the numeric values for permissions: read (4), write (2), and execute (1).
- The final permission should be **600** (read+write for owner, nothing for group and others).

### Solution
```bash
touch project_keys.txt
chmod 600 project_keys.txt
```

The file permissions show **-rw-------**, indicating:

- Owner has read and write permissions
- Group has no permissions
- Others have no permissions

## Assigning Ownership of Project Resources

Project Phoenix is led by Sarah Chen's development team, with the technical lead **dev_lead** managing the core development work. This user belongs to the **developers** group that you've been working with throughout the week. You need to transfer ownership of all project files and directories to ensure proper access control.

### Tasks
- Change the owner of the **~/project/phoenix_project** directory and all its contents to the user **dev_lead**.
- Change the group owner of the ~**/project/phoenix_project** directory and all its contents to the **developers** group.
### Requirements
- The user owner must be **dev_lead**.
- The group owner must be **developers**.
- The ownership change must apply **recursively** to *all files and subdirectories* within **~/project/phoenix_project**.
- You must use the **chown** command.
### Hints
- The **chown** command can change both user and group at the same time using the **user:group** syntax.
- Look for an option in the chown command that allows it to operate on files and directories recursively. The **man chown** command is your friend.
- Since the files are currently owned by **root**, you will need to use **sudo** to change ownership.

### Solution
```bash
sudo chown -R dev_lead:developers ~/project/phoenix_project
```

## Securing the Main Project Directory

Now that the ownership is correct, you need to set the base permissions for the main project directory, **~/project/phoenix_project**. The policy is as follows: t*he owner should have full control, the group should be able to list files and enter the directory, and outsiders should have no access at all*.

### Tasks
- Set the permissions for the **~/project/phoenix_project** directory.
### Requirements
- The owner (dev_lead) must have read, write, and execute permissions.
- The group (developers) must have read and execute permissions.
- Others must have no permissions.
- Use the **chmod** command to apply these permissions to the **~/project/phoenix_project** directory itself (not recursively).
- Since the directory is owned by **dev_lead**, you may need to use **sudo** to change permissions.
### Hints
- "Execute" permission on a directory allows you to cd into it.
- Calculate the numeric permission value for owner, group, and others.
    - Owner (rwx) = 4+2+1 = 7
    - Group (r-x) = 4+0+1 = 5
    - Others (---) = 0+0+0 = 0

### Solution
```bash
sudo chmod 750 ~/project/phoenix_project
```

## Setting Up Collaborative Permissions for the Dev Team

The development team needs to collaborate effectively within the **~/project/phoenix_project/src** directory. To ensure smooth collaboration, any new file or directory created inside **src** should automatically belong to the **developers** group, not the primary group of the user who created it.

### Tasks
- Set a special permission on the **~/project/phoenix_project/src** directory that forces all new files and subdirectories created within it to inherit the group ownership from the src directory itself (which is **developers**).
### Requirements
- The solution must ensure new files in **~/project/phoenix_project/src** are automatically owned by the **developers** group.
- You must use the **chmod** command to set this special permission.
- You may need to use **sudo** to set permissions on directories owned by other users.
### Hints
- This special permission is called the **"set group ID"** or **setgid** bit.
- You can apply the setgid bit using either **symbolic** (g+s) or **numeric** notation.
- In numeric notation, the setgid bit has a value of 2. It is placed before the standard three permission digits (e.g., 2770).

### Solution
Verify or set the group ownership of the directory to **developers**:
```bash
sudo chown :developers ~/project/phoenix_project/src
```
Set the setgid bit on the directory using chmod:
```bash
sudo chmod g+s ~/project/phoenix_project/src
```
This enables the **setgid** bit, meaning new files and subdirectories created inside will inherit the developers group automatically.