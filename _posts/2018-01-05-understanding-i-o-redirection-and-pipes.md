---
layout : post
title : 'Understanding I/O Redirection and Pipes'
tags : [RHCSA, RHCSA_mod, Using Essential Tools, Understanding I/O Redirection and Pipes]
categories: [RHCSA]
---


## I/O Redirection

File
Descriptors:

|            |    |   |    |                 |    |                                                                                     |
| ---------: | :-: | :-: | :-: | :-------------- | :-: | :---------------------------------------------------------------------------------- |
|  **STDIN** | \- | 0 | \- | Standard Input  | \- | Represents the "file" for the Standard Input Device (generally Keyboard).           |
| **STDOUT** | \- | 1 | \- | Standard Output | \- | Represents the "file" for the Standard Output Device (generally the Monitor).       |
| **STDERR** | \- | 2 | \- | Standard Error  | \- | Represents the "file" for the Standard Output Device (also, generally the Monitor). |

Redirection:

|            |    |        |    |                                                                                                                                |
| ---------: | :-: | :----- | :-: | :----------------------------------------------------------------------------------------------------------------------------- |
|  **STDIN** | \- | \(<\)  | \- | Feeds the file to the right of the "\<" as input to the command on the left.                                                   |
| **STDOUT** | \- | \(>\)  | \- | Stores the output of the command to the left of the "\>" to the file indicated on the right. *OVERWRITES* the mentioned file.  |
| **STDOUT** | \- | \(>>\) | \- | Stores the output of the command to the left of the "\(>>\)" to the file indicated on the right. *APPENDS* the mentioned file. |
| **STDERR** | \- | \(2>\) | \- | Redirects the errors from the command mentioned on the left to the file on the right. *OVERWRITES* the mentioned file.         |

``` console
$ mail -s hi root < .
$ ls > myFile
$ ls -lh >> myFile
$ grep hi * 2> /dev/tty6
```

1 - *mail* is a simple command used to send messages. The command
expects the message to terminate with a ".", so we feed it directly to
the command, instead of providing any input.

4 - The STDERR is redirected to tty6 (a virtual terminal connected to
the host). Can also be diverted to a file if needed, such as an
errorLog.

## Piping

The command `$ ps aux` shows us the overview of all the running
processes on the host. However, it’s too long to view all at once. In
such situations, or wherever we need to feed the output of the first
command to the input of the second command, we use the pipe operator.
The command would then be `$ ps aux | less`.

The difference in the usage of the piping and redirection operators is
that Pipe is used to pass output to another program or utility, while
Redirect is used to pass output to either a file or stream.
