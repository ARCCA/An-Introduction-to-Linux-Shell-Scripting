---
title: "Functions and External Tools"
teaching: 25
exercises: 10
questions:
- "How to create functions for easy repeated access to common tasks?"
objectives:
- "Understand the syntax of Bash functions and how we can use them in our scripts."
- "Understant how we can assing the output of Bash commands to variables and use them in our scripts."
keypoints:
- "Functions help us pack a set of operations with a single label."
- "Generally, Bash functions do not return values."
- "The output of Bash commands like *cat*, *ls* or *find* can be assigned to a Bash variable using the construct **VAR=$(command)**"
---

## Functions
Functions allow tasks, corresponding to a number of individual operations, so be 
represented as a label. 

Generally, a function takes one or more variables as input and in most programming
languages can potentially return a value. They can be thought of as a way of creating
user-defined commands which can be repeatedly used to process different sets of
input. A typical bash function has the following structure:

~~~
function function_name {
<commands>
}
~~~
{: .language-bash}

Couple of key points regarding bash functions:
- Unlike most programming languages, there's no easy way of returning a value from a
function in Bash (there are various techniques for achieving this but are beyond the
scope of this tutorial). In other programming languages it is common to have 
arguments passed to the function listed inside the brackets (). In Bash they are 
there only for decoration and you never put anything inside them.
- The function definition ( the actual function itself) must appear in the script 
before any calls to the function.

The following code shows an example of a simple user-defined function, and how it is 
used by a script.
~~~
#!/bin/bash

# Define a simple function that simply prefixes any string it receives with the current date
function datestamp {
  # The first variable passed to the function is stored as $1, as
  # in the case of command line arguments
  STR_RECV=$1

  #Store the current date in a suitable format
  DATE_FMT=$(date +'%d/%m/%y')

  #Print out the combined date stamp plus string
  echo "${DATE_FMT}; ${STR_RECV}"
}

# Call the function with an example string
datestamp "Here is some text"
~~~
{: .language-bash}

> ## Exercise
> Create a script that allows you to pass a series of strings to it and prefix each one with the current date and time. This sort of operation can be useful when logging messages from your code so you can tell exactly when a potential problem occurred. Can you read in a file and prefix each line of the file with the date?
> > ## Solution
> > ~~~
> > #!/bin/bash
> > 
> > # Define a simple function that simply prefixes any string it receives with the current date
> > function datestamp {
> >   # The first variable passed to the function is stored as $1, as
> >   # in the case of command line arguments
> >   STR_RECV=$1
> > 
> >   #Store the current date in a suitable format
> >   DATE_FMT=$(date +'%d/%m/%y-%H:%M:%S')
> > 
> >   #Print out the combined date stamp plus string
> >   echo "${DATE_FMT}; ${STR_RECV}"
> > }
> > 
> > ## Solution 2
> > numargs=$#
> > COUNTER=1
> > 
> > for i in $(seq 1 $numargs); do
> >   datestamp ${COUNTER}
> >   COUNTER=$(( $COUNTER + 1 ))
> > done
> > ~~~
> > {: .language-bash}
> >
> >  This script should produce an output similar to:
> > <pre style="color: silver; background: black;">
> > $ bash functions_01_solution_01.sh command1 command2 command3
> > 14/10/20-11:14:34; 1
> > 14/10/20-11:14:34; 2
> > 14/10/20-11:14:34; 3
> > </pre>
> > 
> > An alternative solution could be to read the commands from a text file (e.g. *date_example_input.txt*):
> > ~~~
> > #!/bin/bash
> > 
> > # Define a simple function that simply prefixes any string it receives with the current date
> > function datestamp {
> >   # The first variable passed to the function is stored as $1, as
> >   # in the case of command line arguments
> >   STR_RECV=$1
> > 
> >   #Store the current date in a suitable format
> >   DATE_FMT=$(date +'%d/%m/%y-%H:%M:%S')
> > 
> >   #Print out the combined date stamp plus string
> >   echo "${DATE_FMT}; ${STR_RECV}"
> > }
> > 
> > ## Solution 2
> > cat date_example_input.txt | while read line; do
> >     datestamp $line
> > done
> > ~~~
> > {: .language-bash}
> >
> >  This script should produce an output similar to:
> > <pre style="color: silver; background: black;">
> > $ bash functions_01_solution_02.sh
> > 14/10/20-11:14:34; 1
> > 14/10/20-11:14:34; 2
> > 14/10/20-11:14:34; 3
> > </pre>
> >
> {: .solution}
{: .challenge}

## Integrating external tools
By now we have covered the most commonly used features of the Bash syntax. But one of the most useful aspects of shell scripting is the ability to integrate shell commands and either pass arguments to them or take output from them to use in the script. 

In Bash an external command is run by enclosing it within a *$(    )* construct.
~~~
#!/bin/bash

# Invoke the external command ‘ls’ to display the contents of,
# a directory and store that as a shell string.
DIRCONT=$(ls -1)

# Loop over the contents of the directory and print each file found
# to the screen.
for filename in ${DIRCONT}; do
    echo "File Found: ${filename}"
done
~~~
{: .language-bash}

In this example instead of explicitly specifying the files we want to look at we have dynamically obtained a list of the contents of the current directory. 

This means we don't have to then check to see whether a file exists and we can run the same code on different groups of files without modification.

This provides a useful template for other commands to be inserted into the for loop. Instead of just printing out the name of the file we could read in the contents using the cat command, copy it to another location, or append some rows of figures.

~~~
#!/bin/bash

#Find all the files in a directory hierarchy that are marked as executable
for file in $(find -executable); do
  echo "File found: ${file}"

  # Change file permissions so that all users can execute the file
  # chmod go+r ${file}
done
~~~
{: .language-bash}

The find tool is another shell command that descends a directory hierarchy and returns a list of files depending on such categories as name, location, type, and many other options. 

In this case we have a hypothetical situation where only the current user has permission to execute files and we want to give everybody permission. Instead of changing the permissions of every file by hand, this simple script automates the task. 

Note the chmod command has been commented out to prevent accidental permission changes. Only uncomment it if you’re certain you understand how it works.

> ## Exercise
> Try creating a set of subdirectories containing named files, each one with a list of words. Use the find command to retrieve only those matching a certain pattern and then read the words into a variable and print them out.
>
> > ## Solution
> > ~~~
> > #!/bin/bash
> > 
> > mkdir script_test1
> > mkdir script_test2
> > 
> > echo "text1" > ./script_test1/text1
> > echo "text2" > ./script_test2/text2
> > 
> > COUNTER=0
> > 
> > #Find all the files in a directory hierarchy that are marked as executable
> > for line in $(find -iname text*); do
> >     LINECAT=$(cat $line)
> >     MY_ARRAY[$COUNTER]="${LINECAT}"
> >     COUNTER=$((COUNTER + 1))
> > done
> > echo "Array contents ${MY_ARRAY[@]}"
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}


## More tools: sed and awk


### awk simple examples

We previosly show you examples of using [``grep``][grep-manual], a very powerful
tool specialized in searching one or more input files for lines containing a match
to a specified pattern. There are two additional tools worth mentioning:
[``sed``][sed-manual] and [``awk``][awk-manual]. Both tools specialized in text
parsing and general text processing. Although an in-depth explanation of how to
use them is beyond the scope of this course, we would like to provide you with a
couple of examples we find useful to demonstrate their functionality and tease you
into finding out more.

Let us start with ``awk``. Consider the following document in the `data` directory:

```bash
$ cd data
$ cat amino-acids.txt 
Alanine         Ala
Arginine        Arg
Asparagine      Asn
Aspartic acid   Asp
Cysteine        Cys
Glutamic acid   Glu
Glutamine       Gln
Glycine         Gly
Histidine       His
Isoleucine      Ile
Leucine         Leu
Lysine          Lys
Methionine      Met
Phenylalanine   Phe
Proline         Pro
Serine          Ser
Threonine       Thr
Tryptophan      Trp
Tyrosine        Tyr
Valine          Val
```

We can see it is a file composed of two columns, with aminoacids long and short names.
We can send the files content to awk and manipulate it. For example, say we are 
interested only in the first column:

```bash
$ cat amino-acids.txt | awk '{print $1}'
Alanine
Arginine
Asparagine
Aspartic
Cysteine
Glutamic
Glutamine
Glycine
Histidine
Isoleucine
Leucine
Lysine
Methionine
Phenylalanine
Proline
Serine
Threonine
Tryptophan
Tyrosine
Valine
```

``awk`` is a programming language which many powerful tools. In the simple example
above the section within `'{}'` is the *program* defining the actions to be performed on the input, in this case, to `print` column `$1`. Don't get confuse with bash
interpretation of `$1` (first argument passed to a script), for ``awk`` it represents
a column number. Try to to extract column 2 instead and even swap columns 1 and 2
positions.

A slightly more complex and maybe useful example. Consider the contents of 
`animal-counts` in the the `data` directory. We can see that it is a CSV file
(comma separated values).

```bash
$ cd data/animal-counts
$ cat animals.txt 
2012-11-05,deer,5
2012-11-05,rabbit,22
2012-11-05,raccoon,7
2012-11-06,rabbit,19
2012-11-06,deer,2
2012-11-06,fox,4
2012-11-07,rabbit,16
2012-11-07,bear,1
```

If we try to extract the first column as before, we quickly find out that it doesn't
work:

```bash
$ cat data/animal-counts/animals.txt | awk '{print $1}'
2012-11-05,deer,5
2012-11-05,rabbit,22
2012-11-05,raccoon,7
2012-11-06,rabbit,19
2012-11-06,deer,2
2012-11-06,fox,4
2012-11-07,rabbit,16
2012-11-07,bear,1
```

The problem is that by default ``awk`` expects a space as file separator, but in this
case we have a `,`. Fortunately ``awk`` is flexible and let us define a new symbol
to use as file separator. This needs to be defined in a special section of the ``awk``
script, the `BEGIN` section:

```bash
$ cat data/animal-counts/animals.txt | awk 'BEGIN{FS=","} {print $1}'
2012-11-05
2012-11-05
2012-11-05
2012-11-06
2012-11-06
2012-11-06
2012-11-07
2012-11-07
```

Say that we want to know the total number of animals recorded in our file. For this
we need to modify our script to perform a summation of every element in column 3 and
report the final result.

```bash
$ cat data/animal-counts/animals.txt | awk 'BEGIN{FS=","} {sum+=$3} END{print sum}'
76
```

In the above example we can see the use of the three main sections of an ``awk`` 
program: the `BEGIN` and `END` sections and the main script. There are many many
more things that can be accomplished with ``awk`` but hopefully these simple examples
gave you a taste of what is possible.

### sed simple examples

sed is a stream editor. A stream editor is used to perform basic text transformations on an input stream (a file or input from a pipeline). 

Usual syntax

```bash
$ sed SCRIPT INPUTFILE...
```

Consider the `animals.txt` file in the `data` directory:

```bash
$ cd data
$ cat animals.txt 
2012-11-05,deer
2012-11-05,rabbit
2012-11-05,raccoon
2012-11-06,rabbit
2012-11-06,deer
2012-11-06,fox
2012-11-07,rabbit
2012-11-07,bear
```

Imagine that we made a mistake and *deer* wasn't the correct category, maybe we 
should use *elk* instead. Amending potentially hundreds of files could be very
time consuming but with ``sed`` this task can be performed in a few seconds:

```bash
$ sed 's/deer/elk/' animals.txt 
2012-11-05,elk
2012-11-05,rabbit
2012-11-05,raccoon
2012-11-06,rabbit
2012-11-06,elk
2012-11-06,fox
2012-11-07,rabbit
2012-11-07,bear
```

By default ``sed`` doesn't modify the input file. You can check by printing again
content of `animals.txt`. If we wanted to save the output of ``sed`` we have two
options:

```bahs
$ sed 's/deer/elk/' animals.txt > animals-fixed.txt
```

or modify the file in place (check ``sed --help``), but be careful as this could
destroy the original file if you make a mistake:

```bash
$ sed -i 's/deer/elk/' animals.txt
```

But ``sed`` has many many more useful functions (check the [manual][sed-manula]),
for example:

- Print a specific line:

  ```bash
  $ sed -n '45p' file.txt
  ```

- Delete a specific line:

  ```bash
  $ sed -n '10d' file.txt
  ```

- Appending text after a line. 

  ```bash
  $ sed '2a hello there' file.txt
  ```
- Insert text before a line
  ```bash
  $ sed '2i hello there' file.txt
  ```

Things can get more complex. But hopefully the above examples can give you an 
indication of what is available on Linux and the command line.


[grep-manual]: https://www.gnu.org/software/grep/manual
[sed-manual]: https://www.gnu.org/software/sed/manual
[awk-manual]: https://www.gnu.org/software/gawk/manual/