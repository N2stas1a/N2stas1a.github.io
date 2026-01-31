
---
date:    '2026-01-17'
title:   'Users groups and sudo'
summary: "RHCSA note for managing users and groups"
tags:    [RHCSA]
series:  [RHCSA]
ShowToc: false
---

Red Hat Enterprise Linux (RHEL)

This chapter explains how to manage users and groups in a RHEL.

#1 Overview

While users could be either people (accounts tied to physical users)
or accounts that exist for special applications to use, groups and logical
exspressions of organization, tying user together for a common purpose.
Users within a group share the same permissions to read,write or execute 
files owned by that group.
Login as root gives you maximum priviliges during the command execution.
If you execute destructive commands it's will be executed from the root
directly, but sudo is used for administrative power execution for one
specific command and logout as user after. Sudo gives an option to track
user command execution as "user switched to root and executed X command".

#2 Inspecting Identity and Group membership

'''Id'''
uid=1001(ananas) gid=1001(ananas) groups=1001(ananas),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023'''

'''Id username

While creating, new user enters in a primary group with its name. Primary group is intened to manage files which are created by this user.
Also, user can enter supplementary groups which are allow to share files
between users by adding users in a supplementary groups.

Based on example presented above
'''uid=1001(ananas) gid=1001(ananas) groups=1001(ananas),10(wheel)'''
output could be exaplined as uid=id and username, gid= primary group name, groups = supplementary groups user included in.

#3 Creating user(useradd)

'''useradd alise
useradd -m bob
useradd -m -s /bin/bash carol
useradd -m -c "Carol Admin" carol2'''

'''useradd -m''' option is used to created user home directory.'''

'''useradd -s /bin/bash - option is used to define user shell.'''

'''useradd -c "Carol Admin" carol2 is used to write GECOS comment as a
default user info.

/etc/default/useradd gives a top level descriprion of the user creation
settings, as a /etc/login.defs allows to precisely configure user settings - UID, GID, password settings etc.

#4 Modyfying groups

'''usemod -aG wheel alise''' could be verified using command

'''grep "alise" etc/group'''

'''wheel:x:10:ananas,alise
alise:x:1012:'''

Alise's user groups are listed above.

'''usermod -L alise
usermod -U alise'''.

-aG option matters in case of adding extra suplementary groups to the user. In case of -G option new listed groups are added to the existing ones, -aG option used only.
-L option is locking user's password. This puts a '!' in front of the encrypted password, effectively disabling
the password.
- U - unlock user's password.

P.s. In case of password absence for the locked user, '''usermod -p'''
option is used to set a password which is automatically disabling account
lock.

'''usermod -s /bin/bash alice'''
 
 verified with a  ''' grep "alise" /etc/passwd/'''

'''alise:x:1009:1012::/home/alise:/bin/bash'''.

#5 Deleting users (userdel)


root@TestBarbarys01 home]# userdel -r bob
[root@TestBarbarys01 home]# ls -ld bob
ls: cannot access 'bob': No such file or directory

[root@TestBarbarys01 home]# userdel alise
userdel: user 'alise' does not exist
[root@TestBarbarys01 home]#'''

-r option recursevely removes files from the user directory with a user home directory itself. -r option removes
file related to this certain deleted user only. It's not able to delete files in the other file system (which could
be removed manually only).

#6 Сreating groups (groupadd)

'''[root@TestBarbarys01 ~]# groupadd devops
[root@TestBarbarys01 ~]# groupadd -g 1050 appteam'''
groupadd -g 1050 appteam

Every group (as user) receiving unique free ID which is listed above the 1000 (for non "system" users). -typically. 
parameters are defined in a etc/login.defs [check]
-g -The numerical value of the group's ID
.
#7 Password management [passwd]

“locking” means a temprorary log in password lock for a user.
Account expiration could be set in a etc/login.defs. Account is disable after expire date.Lock-is a temporary block for the user account.


'''root@TestBarbarys01 ~]# passwd -l alise
passwd: password changed.
[ananas@TestBarbarys01 posts]$ sudo -i
[root@TestBarbarys01 ~]# exit
logout
[ananas@TestBarbarys01 posts]$ su - alise
Password:
su: Authentication failure
[ananas@TestBarbarys01 posts]$ sudo -i
[root@TestBarbarys01 ~]# passwd -u alise
passwd: password changed.
[root@TestBarbarys01 ~]# exit
logout
[ananas@TestBarbarys01 posts]$ su - alise
Password:
Last login: Sat Jan 31 23:29:57 CET 2026 on pts/1
Last failed login: Sat Jan 31 23:34:26 CET 2026 on pts/1
There was 1 failed login attempt since the last successful login.
su: warning: cannot change directory to /home/alise: Permission denied
-bash: /home/alise/.bash_profile: Permission denied.'''

While lock user account is done, it's impossible to log in into this user acc.
After account unclock, log in is becomes available.

#8  Core Account Files: /etc/passwd and /etc/shadow

etc/shadow file is keeping user passwords in a hashed format.

'''[ananas@TestBarbarys01 etc]$ ls -ld /etc/shadow
----------. 1 root root 1089 Jan 28 22:08 /etc/shadow'''

File etc/shadow is blocked for a editing for any types of users. It could prevent data loss or damage due to attacks or reading by non-authorised persons.

etc/passwd keeps user "sensetive" info as a name, UID,GID,GECOS comments, user home dir and user shell (e.g bash)

Typical /etc/passwd format: name:x:UID:GUI:comment:home:shell:

'''ananas:x:1001:1001::/home/ananas:/bin/bash
student:x:1003:1003::/home/student:/bin/bash
user1:x:1008:1011::/home/user1:/bin/bash
carol:x:1011:1014::/home/carol:/bin/bash
carol2:x:1012:1015:Carol Admin:/home/carol2:/bin/bash
loh2:x:1013:1016:loh:/home/loh2:/bin/bash
user12:x:1014:1017::/home/user12:/bin/bash'''

Password hashes are stored in /etc/shadow primarily for a security reasons.
It'snon editable file to prevent cryptography attack and unauthorised users access.

#9 Sudo basics: sudoers and visudo

'''[root@TestBarbarys01 etc]# visudo
visudo: /etc/sudoers.tmp unchanged
[root@TestBarbarys01 etc]# visudo -c
/etc/sudoers: parsed OK'''

Visudo allows to edit the sudoers file in a safe fashio. Visudo lock the sudoers
file against of multople simultaneous edits, provides basic sanity check and
checks for parse errors.

'''
[root@TestBarbarys01 ~]# id alise
uid=1015(alise) gid=1051(alise) groups=1051(alise)
[root@TestBarbarys01 ~]# usermod -aG wheel alise
[root@TestBarbarys01 ~]# id alise
uid=1015(alise) gid=1051(alise) groups=1051(alise),10(wheel)'''

#10 Troubleshoot Cheatset

"User can not sudo" - group membership must be checked.
'''
[root@TestBarbarys01 ~]# id alise
uid=1015(alise) gid=1051(alise) groups=1051(alise)'''


Check syntax with a '''visudo -c'''

[root@TestBarbarys01 etc]# visudo -c
/etc/sudoers: parsed OK'''

"User can not login" - 

Check the user shell in a /etc/passwd file.

'''ananas:x:1001:1001::/home/ananas:/bin/bash'''

User account lock:


'''root@TestBarbarys01 ~]# passwd -l alise
passwd: password changed.
[ananas@TestBarbarys01 posts]$ sudo -i
[root@TestBarbarys01 ~]# exit
logout
[ananas@TestBarbarys01 posts]$ su - alise
Password:
su: Authentication failure'''

'Authentification failure' error is active.

"Group change not efective" - 



