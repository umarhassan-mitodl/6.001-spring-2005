---
content_type: page
description: Installation on Windows OS
draft: false
learning_resource_types:
- Tools
ocw_type: CourseSection
parent_title: Tools
parent_type: CourseSection
parent_uid: 89775783-1f04-f40c-3f68-ee415df41c28
title: "6.001 Scheme for Windows\xAE Machines"
uid: 97837e0b-dc4b-1583-8742-34ff71ef5fec
---
## Installation on Windows NT®, Windows® 95, Windows® 2000, Windows® XP

**Warning** for Windows® 95 users: In order to be able to print from Edwin you must be sure that your default printer was installed with the "Allow printing from DOS" option turned **on**.

**Disclaimer**: While it may be possible to install Scheme on Windows® 98 or Windows® ME, we do not support these operating systems and can only offer limited help. We believe that this Scheme will work on Windows® 2000 and Windows® XP, but if you experience difficulties in using these systems, please let us know. Caveat Hacker.

MIT Scheme requires two directories:

- One for holding the Scheme system itself. This can be placed anywhere you like (but no spaces are allowed in the directory name!), and requires about 15 Megabytes of disk space. Before downloading the installation program, be sure you have 25 Megabytes free on the disk where you plan to place MIT Scheme.
- One for holding problem sets. This, too, can be placed anywhere you like (but no spaces are allowed in the directory name!). Over the course of the semester you should assume that it will grow to be about 5 Megabytes (maybe). There will be two subdirectories: PSets (where you will install the problem sets as they are distributed) and Work (where you will write your solutions).

## Installation Instructions

1. **Download** the Scheme installer, ({{% resource_link "2c333a5c-dc9d-4d61-9916-ceb949cda3f9" "EXE - 10.8 MB" %}}), into a temporary directory of your choice. This file is about 11 megabytes long.
2. Run the install program, win32-6001.exe
3. The InstallWizard will ask you some questions; we suggest that you accept the default for installing the MIT Scheme system itself.
4. The installer will ask you where you want to install the applications database. This is the location that you will store problem set code and your own work, and defaults to c:\\u6001. The installer will create two subdirectories named PSets (to hold the problem sets in the form they are distributed) and Work (where you should modify the problem sets to produce your solutions).
5. You can then delete the install program.

## Installing Problem Sets

Problem sets will be distributed as ZIP files. In order to install them, you will need to be able to "unzip" them on your machine. You can use the shareware program [WinZip®](http://www.winzip.com/), which provides a graphical user interface as well as integration into both Netscape® and Microsoft® browsers.