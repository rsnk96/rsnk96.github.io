---
layout: post
title:  "Linux Commands for an intermediate level user"
date:   2017-01-17
desc: "Some linux commands that help you move beyond being a beginner at Linux"
keywords: "linux, ubuntu, terminal"
categories: [Linux]
tags: [Linux]
icon: icon-centos
---

# Linux Commands for an intermediate level user

Hello everyone. This is my first post for the year, and I thought I'd share some useful linux commands that every frequent Linux user must be comfortable with

They've been written in the form of short notes, because that's the way I took them down at the FOSS Workshop at IIT Madras.
Have Fun!

## Chmod stuff
* directory user group permission
<br>    `d`   directory
<br>    `r`   read
<br>    `w`   writeable
<br>    `x`   executable

* chmod   change file modes / access control list
    o   others
    u   user
    g   group

* Ex:
    `chmod o+w filename.exe`: add w flag

    `chmod o-w filename.exe`: remove w flag
    
    `chmod o-r,u-r,g-r filename.exe`:  change permissions of others, user,grp

## Touch
`$ touch`

changes the timestamp of the file. Used to create new file

## Vim Tut
* When file started, press `i` to go to insert mode, wherein you can edit text in the file
* If you want to escape this, simply hit `Esc`. Now you will be in command mode
* In command mode, for special commands, hit colon followed by your command
* To exit after saving, `:wq`. To quit without saving, `:q!`

## Pipes in linux
* Represent streams of data
* Three streams in linux, stdout, stdin, stderr
* You can use them in linux. One way is using `>`, which implies direction. Ex: $ echo "Out" > test.data
* Above, test.dat will be overwritten. So be careful. To append insteadm use `>>` instead of `>`
* You can output any terminal command to a file using `>`. For instance, `ls -l > test.dat`

* ### Pipe Director (`|`)
    * Redirects output of one command as input of the next command
    * The pipe director command redirects the output as stdin to whatever is on LHS
    * Ex: `$ echo "pipe" | cat`
    * This is better than backquote (the `) as the backquote was used in the older shell and has varying values in unicode/etc

## Grep
* Stands for GNU Regular Expression
* You can search for an expression in something. Ex: If you have a file `demo.dat`, and you want to search for Chennai, then
    
    `cat "demo.dat" | grep "Chennai"`
* If you want it to be case insensitive, use the `ignore case` flag given by `-i`
    
    `cat "demo.dat" | grep -i "Chennai"`
* The `-v` flag will exclude from the results the regex. So, below, it'll print all lines except the ones containing Chennai
    
    `cat "demo.dat" | grep -v "Chennai"`
* The `-o` flag will only show the match, not the entire line that contains it. Ex:
    
    `cat "demo.dat" | grep -i -o "chennai"`
    will output `Chennai` as the match is case insensitive, and it has to display only the match
* GREP recursive search - search entire directory for file. Indicate with the flag `-R`. You can also indicate line number of the result using the `-n`
    Ex: If we have a folder `test_folder` 

    `grep -nR "Chennai" test_folder`

    (You can group `-n -R` to `-nR`)
* The biggest advantage of pipes are in the fact that you can keep piping output of one command as input of the next. For instance, the last awk command below is to be greped, and only 'chennai is needed'

    `cat demo.dat | awk '($4>27 && $3<35){print $1,$3}' > myOutput.txt | grep -i "chennai"`

## AWK command
* It executes a code on every line
* You have to give the code snippet within single quotes for this command
* Can be used for terminal spreadsheet management and display
    Ex: You have a tab separated datasheet `demo.dat`, and we want to print only the first two columns

    `cat demo.dat" | awk '{print $1,$2;}'`
* You can also add conditions (like if conditions) so that you will output only those lines which satisfy the conditions. To do this, add the condition in `()` preceeding the `{}` within the single quotes
    
    `cat demo.dat | awk '($4>27 && $3<35){print $1,$3}'`
    
    will print only those records where the fourth column is greater than 27 AND the 3rd column is greater than 35
* The advantage of piping is that you can cascade them. For instance, in the above command, if we want to put the output to some file

    `cat demo.dat | awk '($4>27 && $3<35){print $1,$3}' > myOutput.txt`


## Sort
* You have to give the `-n` flag if you want to interpret the sort basis as a number and not a string
* Specify the key using `-k`. The key is the column it will use to sort
* Reverse sort can be done using just `-n`
* Ex:

    `cat demo.dat | sort -n -k 3`
* ### `head` and `tail` command
    * Use `-n` to specify the number of lines
    * If not given, displays ten lines
    * Head displays first k lines, Tail displays last k lines
    * Ex: Display the top two results from above

        `cat demo.dat | sort -n -k 3 | head -n 2`
* ### Word and Line count (`wc` or `wc -l`)
    * Ex: Count number of records above

        `cat demo.dat | sort -n -k 3 | wc -l`

## Full-power find and replace: `sed` command
* Ex: Seach for Chennai and replace all occurences with Madras (Indicated by `/g`)

    `cat demo.dat | sed 's/Chennai/Madras/g' `

## Splitting at a delimiter
* Specify delimiter using `-d`
* Let emails.dat be a file with one email-ID per line
* Can use only a single character as delimiter. We can also specify which field we want. Ex: we want only the usernames

    `cat emails.dat | cut -d '@' -f 1`

* Suppose we want to see the email is of a .com or .org or .in, then,,

    `cat emails.dat | cut -d '@' -f 2 |cut -d '.' -f 2`

# Bash Scripts
* Start with `#!/bin/bash`
* Execute commands sequentially
* `$1`: First argument. `$2`: Second argument
* While accessing any variables in a bash script, you have to enclose them within quotes. Ex:

    ```
    #!/bin/bash
    in="$1"
    output = "$2"
    # printing united cities
    cat "$in" |grep -i "united" > "$output"
    echo "printed united"
    cat "$in" |grep -i "africA" >> "$output"
    echo "Completed"
    ```

# Open any file with default application from terminal
Simple use `eog`. Ex: `eog as.jpg` will open `as.jpg` with your default image viewer
