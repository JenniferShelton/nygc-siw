---
title: "Navigating Files and Buckets"
teaching: 10
exercises: 10
questions:
- How can I move around on the computer/vm?
- How can I see what files and directories I have?
- How can I specify the location of a file or directory on the computer/vm?
- How can I specify the location of a file or directory in a bucket?
objectives:
- Translate an absolute path into a relative path and vice versa.
- Construct absolute and relative paths that identify specific files and directories.
- Use options and arguments to change the behaviour of a shell command.
- Demonstrate the use of tab completion and explain its advantages.
keypoints:
- The file system is responsible for managing information on the disk.
- Information is stored in files, which are stored in directories (folders).
- Directories can also store other directories, which then form a directory tree.
- The command `pwd` prints the user’s current working directory.
- The command `ls [path]` prints a listing of a specific file or directory; `ls` on its own lists the current working directory.
- The command `cd [path]` changes the current working directory.
- Most commands take options that begin with a single `-`.
- Directory names in a path are separated with `/` on Unix.
- Slash (`/`) on its own is the root directory of the whole file system.
- An absolute path specifies a location from the root of the file system.
- A relative path specifies a location starting from the current location.
- Dot (`.`) on its own means ‘the current directory’; `..` means ‘the directory above the current one’.
---

## The Filesystem

## Buckets

On your computer files are often stored "locally" on that computer in a directory. On the cloud permanent storage areas are called a “bucket.” The console that we are using is running on an ephemeral virtual machine. We will copy files to our vm or read them from the bucket to use them. Any file we create or modify in our vm will be deleted when we turn off the vm. Users can  use a bucket to save files needed for analysis after the vm is stopped.


> ## CLI typing hints
> - <kbd>Tab</kbd> : autocompletes paths (use this for speed and to avoid mistakes !!)
> - <kbd>↑</kbd>/<kbd>↓</kbd> arrow : moves through previous commands
> - <kbd>Ctrl</kbd><kbd>a</kbd> : goes to the beginning of a line
> - <kbd>Ctrl</kbd><kbd>e</kbd>: goes to the end of the line
> - short flags generally `-` followed by a single letter
> - long flags generally `--` followed by a word
> - flags are often called options in manuals (both terms are correct)
> - command/program will be used interchangeably (a whole line of code is also called a command)
> - To list your past commands: Type `history` in the command line
{: .testimonial}
