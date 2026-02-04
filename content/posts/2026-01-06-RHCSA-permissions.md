---
date: "2026-02-04"
title: "RHCSA permissions"
summary: "RHCSA note for managing permissions"
tags: [RHCSA]
series: [RHCSA]
ShowToc: false
---

# Overview

File permissions are detemine who can access the directed files and directories.

Linux permissions could be viewed using command `ls -l` - list long format.
Command execution result will represent you a file with a preffix type: `rwxrwxrwx` +
rest of info as a file and group owner,file modification date and file name.
File access is represented on a 3 levels of the users who could have possibility to
_read, write, execute_ intened file. These access groups consist of _owner,group, others_.
"Owner" - file owner, "group" - group which owns a file, "others" - everyone else.

# 2 How to read permissions

Linux permissions could be viewed using command

```bash
ls -l # list long format
```

So, example rules like

```bash
-rw-r--r--
```

could be explaines as:

- `"-"` - shows a file.
- `"rw-"` - means that't file _owner_ could _read, write file, but NOT execute (as x letter has "-"_
- `"r--"` - _group_ which owns a file could \*read, but CAN NOT write,CAN NOT execute a file
- `"r--"` _others_ can _read, but CAN NOT write, CAN NOT execute_

Second example:

- `"drwxr-x---"` means:
- `"d"` - directory
- `"rwx"` - user who owns a directory CAN _read,write,execute_.
- `"r-x"` - group can _read, CAN NOT write, can execute_
- `"---"` - others _CAN NOT read,CAN NOT write,CAN NOT execute_.

# 3 Ownership and group

Owner and group could be changed in a various ways:

```bash
chown username filename
```

This command is used to change a file owner. Thus,after command.
execution FILE will rewrite an owner to USER.

`chgrp groupname filename`
This command is used to change a group which owns a file. Thus,
executing this command FILE will rewrite GROUP.

`chown` - command is used to change an OWNER and GROUP at the same moment. In
the end, file owner and group will be a USER and GROUP.
These changes could be verified using

```
ls -l
```

# 4 Changing permissions

Also, there are 2 ways to change permissions - _numeric_ and _symbolic_ ones.
Chmod command is used to change assigned permissions. There are few ways how to do it:
Symbolic mode:

```
chmod u+x file - adding a posibility to execute a file to the user(file owner).
chmod g-w file - substracts a possibility to write from the group which owns a file.
chmod o=r file - others could only read the file
chmod u=rwx file,g=rx,o= file - that's means USER could *read,write,execute* a file,
group - read and execute, others -  has no permissions to do nothing.
```

## Numeric mode(octal):

There is a possibility to make numeric modifications with a file.
Maximum value is 7 which is consist of:

**4** - read (r)

**2** - write (w)

**1** - execute (x)

So, **rwx** permission is equal to **7**

```bash
chmod 644 file
```

means:

**7-1=6**   - only execution(x) is substracted for user

**7-1-2=4** - execution(x) and read(r) is substracted for group

**7-1-2=4** - execution and read(r) is substracted for others


In the end, file permissions are: **rw-r--r--**

Chmod 600 file is:
7-1(x) = 6(read+write) for user
0 - NO read,write,execute for group
0 - No read,write,execute for others
So, file permissions are: (rw-)(---)(---).

Chmod 755 script.sh:
7 - read,write,execute
7-2(w) = 5 - wite is substracted from the group and others
File permissions are: rwxr-xr-x.

Chmod 700 dir:
7 - full permissions
0 - No read,write,execute for group and others
Directory permissions are: rwx------

#5 Directories vs files

read, write and execute permission could be implemented to the:

- regular files
- directories

Read - allows to view file content and list directory content;
Write - allows file modification/deletion or adding/removing entries(directories);
Execute - allows to run a file and enter a directory.

No execute (x) option does not allows to enter directory.

```
[username@hostname ~]$ ls -l
total 4
-rw-r--r--. 1 username username  98 Jan 11 20:30 1
drw-rw-rw-. 3 username username  59 Jan 11 18:53 labExamples
drwxr-xr-x. 8 username username 161 Jan  6 18:34 N2stas1a.github.io
[username@hostname ~]$ cd labExamples/
bash: cd: labExamples/: Permission denied
[username@hostname ~]$
```

No read(r),but wx options are here. Execute(x) option allows to enter directory, but no read(r) option does not allows to list directory content.

```
[username@hostname ~]$ ls -l
total 0
d-wx-wx-wx. 3 username username  59 Jan 11 18:53 labExamples
drwxr-xr-x. 8 username username 161 Jan  6 18:34 N2stas1a.github.io
[username@hostname ~]$ cd labExamples/
[username@hostname labExamples]$ ls -l
ls: cannot open directory '.': Permission denied
```

#6 Special permissions bits(RHCSA-relevant)

##SUID

SUID - special file permission allowing to execute a file from the _file owner_.

```
[username@hostname labExamples]$ chmod u+s file1
[username@hostname labExamples]$ ls -l
total 4
```

```
-rw-r--r--. 1 username username 297 Jan 11 20:42 1
-rwsrwxrwx. 1 username username   0 Jan 11 20:48 file1
```

##SGID

SGID - permission which allows to the file to be executed from the group that's owns a file. Additionaly, files which are created in a directory will inherit a group where SGID is set.

```
username@hostname ~]$ chmod g+s ./labExamples
[username@hostname ~]$ ls -l
total 8
drwxrwsrwx. 4 username username  44 Feb  4 22:11 labExamples
drwxr-xr-x. 8 username username 161 Jan 27 22:09 N2stas1a.github.io
[username@hostname ~]$ cd ./labExamples/
[username@hostname labExamples]$ ls -ld
drwxrwsrwx. 4 username username 44 Feb  4 22:11 .
[username@hostname labExamples]$ mkdir SGIDdir
[username@hostname labExamples]$ ls -l
total 0
drwx--S---. 2 username username 6 Feb  4 22:23 SGIDdir
```

##Sticky bit

Sticky bit - permission which is restricting file deletion. Only file _owner_ (and
root) are able to delete files within this directory.

```
[username@hostname labExamples]$ mkdir STICKY
[username@hostname labExamples]$ chmod +t STICKY/
[username@hostname labExamples]$ ls -ld STICKY/
drwxr-xr-t. 2 username username 6 Jan 11 21:19 STICKY/
```

##Umask

Umask - is a command to set permissions for files which are created by user in a current runtime. After new umask implementation, permission rules for the newely created files, will be changed.
777 permissions could be over permissive for created files. So, umask could be implemented to substract a permissions.

```
username@hostname labExamples]$ umask
0022
drwxrwxrwx. 2 username username 6 Jan 22 22:30 .
[username@hostname labExamples]$ mkdir exampledir1
[username@hostname labExamples]$ ls -l
total 0
drwxr-xr-x. 2 username username 6 Feb  4 22:11 exampledir1
[username@hostname labExamples]$ umask 077
[username@hostname labExamples]$ mkdir exampledir2
[username@hostname labExamples]$ ls -l
total 0
```

The difference between two files after new umask implementation is showed below.

```
drwxr-xr-x. 2 username username 6 Feb  4 22:11 exampledir1
drwx------. 2 username username 6 Feb  4 22:11 exampledir2
```

##8 Troubleshooting cheatsheet

There is main troubleshooting pricnciples applicable to the permission topic:
„“I can’t access a file” – check the file\directory permissions.
Ls –l option or stat [file] should be applied.
Please check file ownership:
Does your user has a possibility to execute needed directory? – check execution options
Does your user belongs to the group allowed to execute file\directory? – check group using

````id [User]
and
```group [user]
````

“Permission denied on a script” – execution (x) permission letter must be implemented to the file\directory.
“I changed permissions but it still fails” – verify the file path correctness. Verify command and command path correctness.
