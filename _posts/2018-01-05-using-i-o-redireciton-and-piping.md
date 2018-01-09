---
layout : post
title : 'Using I/O Redireciton and Piping'
tags : [RHCSA, RHCSA_mod, Using Essential Tools, Understanding I/O Redirection and Pipes]
categories: [RHCSA]
---


``` console
$ ps aux | awk '{print $2}'
$ ps aux | awk '{print $2}' | sort
$ ps aux | awk '{print $2}' | sort -n
```

The second column ($2) of the `$ ps aux` command contains the Process ID
(PID), and if we only want to filter the output such that only the PID
is shown, we simply use the **awk** filtering utility.

If we want to sort the output of the command, we use the **sort**
utility, but it generally sorts as a string. To sort the output as a
number, we use the option **sort -n**.

If you expect lots of errors for a particular command, but want to
discard all errors and only see the output when successful, then simply
redirect the STDERR to `/dev/null`, which is a special device that
discards all data written to it, i.e., a dustbin for data.

``` console
$ find / -name "*.rpm"
$ find / -name "*.rpm" 2> /dev/null
```

The first command shows all output including errors, but the second
command discards all errors and shows the rest.

``` console
$ some_command > /dev/null 2> &1
```

The above code redirects STDOUT to `/dev/null` thus destroying the
output, and also redirects the STDERR (2\>) to STDOUT (&1). Essentially,
it discards all output - useful when we don’t need the output but only
need the command to execute.

``` console
$ ls / > file_list.txt
$ sort < file_list.txt > file_list_sorted.txt
```

The above command stores the contents of the root directory in
*file\_list.txt*. Then, the second command uses both input and output
redirection\! The input of the sort command is fed from *file\_list.txt*
and the corresponding output sent to *file\_list\_sorted.txt*.
