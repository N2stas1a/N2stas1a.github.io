---
date:    '2026-01-06'
title:   'RHCSA permissions'
summary: "RHCSA note for managing permissions"
tags:    [RHCSA]
series:  [RHCSA]
ShowToc: false
---

Red Hat Enterprise Linux (RHEL)

File permissions are detemine who can access the directed files and directories.

Linux permissions could be viewed using command *ls -l* - list long format. 

Command execution result will represent you a file with a preffix type: "rwxrwxrwx" +
rest of info as a file and group owner,file modification date and file name.
File access is represented on a 3 levels of the users who could have possibility to
*read, write, execute* intened file. These access groups consist of *owner,group, others*. 
"Owner" - file owner, "group" - group which owns a file, "others" - everyone else. 

So, example rules like -rw-r--r--" could be explaines as: 
 "-" - shows a file.
 "rw-" - means that't file *owner* could *read, write file, but NOT execute (as x letter has "-"*
 "r--" - *group* which owns a file could *read, but CAN NOT write,CAN NOT execute a file.
 "r--" *others* can *read, but CAN NOT write, CAN NOT execute*.
 
 Second example:
 "drwxr-x---" means:
 "d" - directory
 "rwx" - user who owns a directory CAN *read,write,execute*.
 "r-x" - group can *read, CAN NOT write, can execute*
 "---" - others *CAN NOT read,CAN NOT write,CAN NOT execute*.
 
 *Ownership and group*

 Owner and group could be changed in a various ways:
 
 chown user:file - command is used to change a file owner. Thus,after command 
 execution FILE will rewrite an owner to USER.
 Chgrp group file - command is used to change a group which owns a file. Thus, 
 executing this command FILE will rewrite GROUP.
 chown - command is used to change an OWNER and GROUP at the same moment. In
 the end, file owner and group will be a USER and GROUP.
 
 These changes could be verified using *ls -l* command or stat [file].
 
 *Changing permissions*
 
 Also, there are 2 ways to change permissions - *numeric* and *symbolic* ones.
 Chmod command is used to change assigned permissions. There are few ways how to
 do it:
 
 Symbolic mode:
 chmod u+x file - adding a posibility to execute a file to the user(file owner).
 chmod g-w file - substracts a possibility to write from the group which owns a file.
 chmod o=r file - others could only read the file
 chmod u=rwx file,g=rx,o= file - that's means USER could *read,write,execute* a file,
 group - read and execute, others -  has no permissions to do nothing.
 
 Numeric mode:
 There is a possibility to make numeric modifications with a file.
 Maximum value is 7 which is consist of:
 4 - read (r)
 2 - write (w)
 1 - execute (x)
 So, rwx permission is equal to "7".
 chmod 644 file - means:
 7-1(x)=6 - only execution(x) is substracted for user
 7-1(x)-2(w)=4 - execution(x) and read(r) is substracted for group
 7-1(x)-2(w)=4 - execution and read(r) is substracted for others
 In the end, file permissions are: rw-r--r--
 
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
 Directory permissions are: rwx------. 
  
 *Directories vs files*

 read, write and execute permission could be implemented to the:
 - regular files
 - directories
 
 Read - allows to view file content and list directory content;
 Write - allows file modification/deletion or adding/removing entries(directories);
 Execute - allows to run a file and enter a directory.

No execute (x) option does not allows to enter directory.

'''bash
[ananas@TestBarbarys01 ~]$ ls -l
total 4
-rw-r--r--. 1 ananas ananas  98 Jan 11 20:30 1
drw-rw-rw-. 3 ananas ananas  59 Jan 11 18:53 labExamples
drwxr-xr-x. 8 ananas ananas 161 Jan  6 18:34 N2stas1a.github.io
[ananas@TestBarbarys01 ~]$ cd labExamples/
bash: cd: labExamples/: Permission denied
[ananas@TestBarbarys01 ~]$'''

No read(r),but wx options are here. Execute(x) option allows to enter directory, but no read(r) option does not allows to list directory content. 

'''bash
[ananas@TestBarbarys01 ~]$ ls -l
total 0
d-wx-wx-wx. 3 ananas ananas  59 Jan 11 18:53 labExamples
drwxr-xr-x. 8 ananas ananas 161 Jan  6 18:34 N2stas1a.github.io
[ananas@TestBarbarys01 ~]$ cd labExamples/
[ananas@TestBarbarys01 labExamples]$ ls -l
ls: cannot open directory '.': Permission denied
'''

 Special permissions bits(RHCSA-relevant)
 SUID - special file permission allowing to execute a file from the * file owner*.
 
'''bash
[ananas@TestBarbarys01 labExamples]$ chmod u+s file1
[ananas@TestBarbarys01 labExamples]$ ls -l
total 4
'''

'''bash
-rw-r--r--. 1 ananas ananas 297 Jan 11 20:42 1
-rwsrwxrwx. 1 ananas ananas   0 Jan 11 20:48 file1
'''

 SGID - permission which allows to the file to be executed from the group that's
 owns a file. Additionaly, files which are created in a directory will inherit a 
 group where SGID is set. 
 
'''bash
[ananas@TestBarbarys01 labExamples]$ ls -l
total 8
i-rw-r--r--. 1 ananas ananas 297 Jan 11 20:42 1
-rw-r--r--. 1 ananas ananas 202 Jan 11 21:07 2
-rwsrwxrwx. 1 ananas ananas   0 Jan 11 20:48 file1
drwx------. 2 root   root     6 Jan 11 16:44 part8
drwxrwsrwx. 2 ananas ananas   6 Jan 11 21:09 SGIDxample
[ananas@TestBarbarys01 labExamples]$ ls -ld SGIDxample/
drwxrwsrwx. 2 ananas ananas 6 Jan 11 21:09 SGIDxample/
'''

 777 permissions could be over permissive for created files. So, umask could be
 implemented to substract a permissions. 


 Sticky bit - permission which is restricting file deletion. Only file *owner* (and
 root) are able to delete files within this directory.

 '''bash
[ananas@TestBarbarys01 labExamples]$ mkdir STICKY
[ananas@TestBarbarys01 labExamples]$ chmod +t STICKY/
[ananas@TestBarbarys01 labExamples]$ ls -ld STICKY/
drwxr-xr-t. 2 ananas ananas 6 Jan 11 21:19 STICKY/
'''

 Umask - is a command to set permissions for files which are created by user.
 777 permissions could be over permissive for created files. So, umask could be
 implemented to substract a permissions. 

 '''bash 
[ananas@TestBarbarys01 labExamples]$ umask 022
[ananas@TestBarbarys01 labExamples]$ mkdir umask3'''

'''bash
[ananas@TestBarbarys01 labExamples]$ ls -ld umaskXample/
drwxr-xr-x. 2 ananas ananas 6 Jan 11 21:20 umaskXample/
[ananas@TestBarbarys01 labExamples]$ umask 077
[ananas@TestBarbarys01 labExamples]$ mkdir umask2
[ananas@TestBarbarys01 labExamples]$ ls -ld umask2
drwx------. 2 ananas ananas 6 Jan 11 21:20 umask2
[ananas@TestBarbarys01 labExamples]$ umask 022
[ananas@TestBarbarys01 labExamples]$ mkdir umask3
'''

 Default maximum rules are: 
 666 (rw-rw-rw-) for files;
 777 (rwxrwxrwx) for directories;
 Umask 022 is applied to the file: 666 - 022 = 644;
 Umask 022 is applied to the directory: 777 - 022 = 755;
 
 Troubleshooting cheatsheet

There is main troubleshooting pricnciples applicable to the permission topic: 
„“I can’t access a file” – check the file\directory permissions.
Ls –l option or stat [file] should be applied. 
Please check file ownership:
Does your user has a possibility to execute needed directory? – check execution options
Does your user belongs to the group allowed to execute file\directory? – check group using '''id [User]''' and '''group [user]''' commands.
“Permission denied on a script” – execution (x) permission letter must be implemented to the file\directory.
“I changed permissions but it still fails” – verify the file path correctness. Verify command and command path correctness. 

