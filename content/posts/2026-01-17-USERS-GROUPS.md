
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

2 Inspecting Identity and Group membership

'''Id'''
uid=1001(ananas) gid=1001(ananas) groups=1001(ananas),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023'''

'''Id username

3 Creating user(useradd)

'''useradd alise
useradd -m bob
useradd -m -s /bin/bash carol
useradd -m -c "Carol Admin" carol2'''

'''useradd -m''' option is used to created user home directory.

[Explain  shell selection and GECOS field]

4 Modyfying groups

-aG option matters in case of adding extra suplementary groups to the user. In case of -G option new listed groups
are added to the existing ones, -aG option used only.
-L option is locking user's password. This puts a '!' in front of the encrypted password, effectively disabling
the password.
- U - unlock user's password. [add explanation from the RHEL lessons]

5 Deleting users (userdel)

-r option recursevely removes files from the user directory with a user home directory itself. -r option removes
file related to this certain deleted user only. It's not able to delete files in the other file system (which could
be removed manually only).

6 Сreating groups (groupadd)

groupadd -g 1050 appteam
Every group (as user) receiving unique free ID which is listed above the 1000 (for non "system" users). -typically 
parameters are defined in a etc/login.defs [check]
-g -The numerical value of the group's ID.

7 Password management [passwd]

What “locking” means in practice [add]
Account expiration could be set in a etc/login.defs. Account is disable after expire date.Lock - is a temporary
block for the user account [add example]

8  Core Account Files: /etc/passwd and /etc/shadow

etc/shadow file is keeping user passwords in a hashed format. File is read-only. [explain]
etc/passwd keeps user "sensetive" info as a name, UID,GID,GECOS comments, user home dir and user shell (e.g bash)
[explain]

'''uid=1001(ananas) gid=1001(ananas) groups=1001(ananas),10(wheel)'''

While creating, new user enters in a primary group with its name. Primary group is intened to manage files which are created by this user.
Also, user can enter supplementary groups which are allow to share files
between users by adding users in a supplementary groups.

Based on example presented above
'''uid=1001(ananas) gid=1001(ananas) groups=1001(ananas),10(wheel)'''
output could be exaplined as uid=id and username, gid= primary group name, groups = supplementary groups user included in.

