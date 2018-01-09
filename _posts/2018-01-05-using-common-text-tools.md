---
layout : post
title : 'Using common text tools'
tags : [RHCSA, RHCSA_mod, Working with Text Files, Using common text tools]
categories: [RHCSA]
---


## cat

The `cat` command prints the entire content of a file on to the
terminal.

## less

Sometimes the `cat` command is unsuitable, like in the case of extremely
large files. In such cases, like the `/var/log/messages`, the default
system log file, using `cat` won’t work as the majority of the messages
would scroll past fast. For such cases, `less` is a better utility.
Search functionality is exactly the same as in the case of vim.

## Head and Tail

### Head

The `head` command by default shows us the first 10 lines of a text
file. To see more or less lines, the `-n` option can be used.

``` console
$ head -n 20 file.txt
```

### Tail

The `tail` command by default shows us the last 10 lines of a text file.
To see more or less lines, the `-n` option can be used.

``` console
$ tail -n 5 file.txt
```

### Combination of head and tail

The combination of these two commands can enable the viewing of text in
between specific line numbers. The command below shows lines 16-20 of
`file.txt`

``` console
$ head -n 20 file.txt | tail -n 5
```

## cut

With the `cut` utility, we can print out a specific column form a text
file. It assumes the columns are separated by Tabs. Which specific
column is to be printed is set by using the `-f` option. For example, to
only print the first column of a text file, we say:

``` console
$ cut -f 1 cities
```

To provide a different delimter, such as ":" we use the `-d` option
followed by the delimiter of our choice.

``` console
$ cut -f 1 -d : /etc/passwd
```

## sort

This command sorts the input provided in the order of the ASCII table.
That means numbers first, captial letters next and finally the lower
case letters.

``` console
$ cut -f 1 -d : /etc/passwd | sort
```

To sort on the basis of a specific
criteria:

|        |    |                                                                                         |
| :----- | :-: | :-------------------------------------------------------------------------------------- |
| **-n** | \- | Sort on the basis of actual numberical value, instead of treating a number as a string. |
| **-f** | \- | Sort in a case insensitive manner.                                                      |

## tr

The `tr` command replaces certain characters with certain other
characters. Thus, it’s frequently used in conjunction with pipes to
modify the output of a command.

``` console
$ echo hello | tr a-z A-Z
HELLO
$ echo hello | tr [:lower:] [:upper:]
HELLO
```
