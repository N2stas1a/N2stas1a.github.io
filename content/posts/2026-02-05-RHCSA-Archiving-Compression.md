---
date:    '2026-02-05'
title:   'Archiving and compressing'
summary: "RHCSA note for managing archives and compressions."
tags:    [RHCSA]
series:  [RHCSA]
ShowToc: false
---

Red Hat Enterprise Linux (RHEL)

This chapter explains how to manage archives and compressions.

# Overview

There is two ways to manage files in Linux OS - compressing and archiving.
Compressing changes file size by decreasing bits quantity from the first file size.
Archiving combines several files into one archiving file with a archiving extension.

Compression and archiving could be combined and used together. Archiving & compressing
is widely used in Linux and out of Linux OS for file transfer,sets of files combined
into one archive e.g. saving logs, decreasing amount of spaces used by files, backuping
file.

# 2 tar Basics


```
[username@hostname labExamples]$ tar -cvf backup.tar ~/etc
#create uncomressed tar archive

[username@hostname labExamples]$ tar -tvf backup.tar
#list content without extracting

[username@hostname labExamples]$ tar -xvf backup.tar
#extract into current directory

[username@hostname labExamples]$ tar -xvf backup.tar -C /tmp/
#extract into specific directory

[username@hostname labExamples]$ ls -l
total 12
-rw-r--r--. 1 username username 10240 Feb  6 00:02 backup.tar
```

```
-c - create a new archive
-t - list the contents of an archive
-x - extract files from an archive
```
