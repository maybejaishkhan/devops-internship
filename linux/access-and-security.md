# Access and Security in Linux

- **Users** Each user has a unique **UID** (User ID).
  - **Root** (UID 0): full system access.
  - **System users**: for services (e.g. `www-data`, `mysql`).
  - **Normal users**: for human users.
- **Groups** Each group has a unique **GID** (Group ID). Users can belong to:
  - One **primary group**.
  - Multiple **supplementary groups**.

## User and Group Management

- Create
  - User: `adduser <user>`
  - Group: `groupadd <group>`
- Change
  - UserPassword: `passwd <user>`
  - UserGroup: `usermod -aG <group> <user>`
  - GroupName: `groupmod -n <new> <old>`
- Delete
  - User: `deluser <user>`
  - Group: `groupdel <group>`
- See
  - UID, GID, Groups: `id <user>`
  - Groups: `groups <user>`
  - UserEntry: `getent passwd <user>`
  - GroupEntry: `getent group <group>`

## **Unix File Permissions**

All Unix-like OSes (Linux, macOS, BSDs etc.) have a very robust system of who is allowed to do what.

When we use the `ls -l` command, we see something like this:

```txt
drwxr-xr-x 2 user1 staff 4096 Jul  7 19:00 myproject/
-rw-r--r-- 1 user1 staff 1024 Jul  7 19:05 document.txt
-rwxr-x--- 1 user2 users  512 Jun 15 09:30 script.sh*
lrwxrwxrwx 1 user1 staff   12 Jul  7 19:10 symlink_to_doc -> document.txt
||--|--|-- | |     |        | |---------   |
||  |  |   | |     |        | |            └── Name
||  |  |   | |     |        | |
||  |  |   | |     |        | |
||  |  |   | |     |        | └──────────── Last Modification (date + time)
||  |  |   | |     |        └──────────── File Size (bytes)
||  |  |   | |     └───────────── Group
||  |  |   | └─────────────────── User
||  |  |   └────── No of Hard Links pointing to this
||  |  └── Others Permissions
||  └───── Group Permissions
|└──────── User Permissions
└─── File Type

```

The first column is for File Type and Permissions (10 characters)

**Character 1 (File Type):** file `-`, directory/folder `d`, symlink `l`, socket `s`, character-device file `c`, block-device file `b`, named pipe `p`.

**Characters 2-10 (Permissions):** 3 sets of 3 characters, representing permissions (read `r`, write `w`, execute `x`, no `-`) for:

- **User/Owner (Characters 2-4):**
    - If `s` appears here instead of `x`, it indicates the **SetUID (SUID)** bit is set (the file will run with the owner's privileges). If `S` appears, SUID is set but execute is not. `chmod u+s file`
- **Group (Characters 5-7):**
    - Applies to users who are members of the file's group.
    - If `s` appears here instead of `x`, it indicates the **SetGID (SGID)** bit is set (the file will run with the group's privileges, or for a directory, new files inherit the directory's group). If `S` appears, SGID is set but execute is not. `chmod g+s file`
- **Others/World Permissions (Characters 8-10):**
    - Applies to all other users on the system.
    - If `t` appears here instead of `x`, it indicates the **Sticky Bit** is set (for directories, only the owner of a file within it can delete or rename that file). If `T` appears, Sticky Bit is set but execute is not. `chmod +t file`

> **Octal Notation**
> A 3 digit octal number is used for denoting permissions as well (1 digit for user, group, others each) where `r = 4`, `w = 2`, `x = 1`.
> So, `755 == rwxr-xr-x`. Special bits are denoted by adding a fourth bit at the start: SUID `s = 4`, SGID `s = 2` and Sticky `t = 1`.

### **Managing Permissions**

1. Change (Access): `chmod <permission> <file or folder>`
2. Change (Owner): `chown <user> <file>` or `chown <user>:<group> <file>`
3. Change (Group): `chgrp <group> <file>`

## Advanced

umask 022

### **Audit and Logs**

1. **Check File Permissions Recursively**: `find /path -type f -perm 777`
2. **Audit User File Access**:

```bash
auditctl -w /etc/passwd -p war -k passwd_changes
ausearch -k passwd_changes
```

## Security

### **ACLs (Access Control Lists)**

- Allow more fine-grained permissions beyond owner/group/others.

```bash
setfacl -m u:bob:rw file
getfacl file
setfacl -x u:bob file
```

**Default ACLs for Directories**

```bash
setfacl -d -m u:bob:rwx project_dir
```

**Remove all ACLs**

```bash
setfacl -b file
```

---

### Advanced Security

#### **SELinux (Security-Enhanced Linux)**

- Provides **mandatory access control** (MAC).
- Labels files and processes with contexts.
- Enforcing, Permissive, or Disabled mode.

```bash
sestatus
ls -Z
chcon -t httpd_sys_content_t file
```

#### **AppArmor (Ubuntu alternative to SELinux)**

- Uses profiles for application confinement.

```bash
aa-status
aa-complain /usr/bin/foo
```
