---
title: "Flow Control"
teaching: 30
exercises: 15
questions:
- "How to control the flow of a Bash program?"
objectives:
- "Understand Bash basic conditional structure."
- "Familiarize with common operators to compare strings and integers."
- "Familiarize with common operators to test existance of files and directories."
keypoints:
- "The basic conditional structure in Bash is built as: *if...then...else...fi* ."
- "Bash has operators specific for string and integer comparisons."
- "Bash also has comparison operators useful to test the existance of files and directories."
---

## The `if` statement
A programming language, even a simple one, requires the ability to change its 
functionality depending upon certain conditions. For example, if a particular file 
exists or if the number represented by a variable is greater than some value then 
perform some action, otherwise perform a different action. In this section we shall
look at ways of determining the flow of a script.

We have already seen how variables can be assigned and printed. But we may want to 
test the value of a variable in order to determine how to proceed. In this case we can
use the if statement to test the validity of an expression. A typical shell comparison
to test the validity of *CONDITION* has the form:
~~~
if [[ CONDITION ]]; then
    echo "Condition is true"
fi
~~~
{: .language-bash}

Where *CONDITION* is typically a construct that uses two arguments which are compared 
using a **comparison operator**. Some of the more common comparison operators used to form
conditions are summarized inthe following table:

|    Arithmetic          ||     String            ||
|-------------------------|---------|--------------|
| **-eq**  | equals       | **=**   | equals       | 
| **-nq**  | not equal to | **!=**  | not equal to | 
| **-lt**  | less than    | **<**   | less than    | 
| **-gt**  | greater than | **>**   | greater than | 
| **-le**  | less than or equal to               ||| 
| **-ge**  | greater than or equal to            ||| 

It is also possible to add an additional default action to be executed in case our 
test is not satisfied, for this we use the `else` statement:
~~~
if [[ CONDITION ]]; then
    echo "Condition is true"
else
    echo "Condition is false"
fi
~~~
{: .language-bash}

For example, we can perform a comparison on two integers. Copy the following command
to a bash script (you can also try executing directly in the terminal similar as we
did with `for` loops) called *comparing-integers.sh*:
~~~
if [[ $1 -eq $2 ]]
then
    echo ${1} is equal to ${2}
elif [[ $1 > $2 ]]
then
    echo ${1} is greater than ${2}
elif [[ $1 -lt $2 ]]
then
    echo ${1} is less than ${2}
fi
~~~
{: .language-bash}

And execute it like this (try also with other numbers):
~~~
$ bash comparing-integers.sh 10 3
~~~
{: .source}

~~~
10 is greater than 3
~~~
{: .output}

We can also compare strings. Copy the following commands to a script called
*comparing-strings.sh*:
~~~
if [[ $1 == $2 ]]
then
    echo strings are equal
else
    echo strings are different
fi
~~~
{: .language-bash}

And execute it like this:
~~~
$ bash comparing-integers.sh dog cat
~~~
{: .source}

~~~
strings are different
~~~
{: .output}

> ## More on Integer and String comparisons
> Consider the following code snippet, it demonstrates some basic string and integer
> comparisons, with branching code depending upon the outcome. We first define two 
> variables *X* and *Y* (you can also use `$1` and `$2` to access arguments passed)
> to the script) and assign them integer values (remember that to Bash they are still
> strings). 
>
> The next step is to build an *if...then...else* construct to test our variables 
> using an arithmetic comparison operator. Specifically, using `-eq` lets Bash know 
> that the values stored in the variables are to be treated as numbers. If *X* is 
> equal to *Y* the script performs one action, if not, then it performs another.
>
> ~~~
> #!/bin/bash
> #Declare two integers for testing
> X=3
> Y=10
> 
> #Perform a comparison on the integers
> if [[ $X -eq $Y ]]; then
>     echo "${X} equals ${Y}"
> else
>     echo "${X} does not equal ${Y}"
> fi
> ~~~
> {: .language-bash}
>
> Now try the following: 
>  - Use some of the other comparison operators and see if your results meet your 
>    expectations. What outcome would you expect when using the string comparison 
>    operators `<` or `>`?
>  - Test different *numbers*, for example, compare 10 and 3; "10" and "3" (including
>    the `"`); "10" " 3" (notice the space in front of the number 3); "40" "3". What
>    kind of results do you obtain?
>
> > ## Solution
> > Notice that in the first case the results are not necessarily as we expect since
> > the characters are compared in alphabetical (ASCII) order since we are using a 
> > string comparison operator. 
> >
> > In the second case we can confirm this by noticing that bash (when instructed to
> > compare the numbers as strings) compares the first character of each string, and 
> > if the one on the left has a lower value then it's true, if greater then it is
> > false; if they're the same, then it compares the second character, etc. 
> {: .solution}
{: .challenge}

A common practical application of `if` statements in programming scripts are is to
add a `help` flag to print some useful information about the script. For example:
~~~
if [[ "$1" == "--help" ]]
    then
    echo "Returns the file with the most number"
    echo "of lines in the list provided"
    echo "To execute:"
    echo "print-larger.sh <list-of-files>"
fi

wc -l $@ | sort -n | tail -1
~~~
{: .language-bash}

The above script **should** print the help message when executed like this:
~~~
print-larger.sh --help
~~~
{: .source}

But you might see an addional error message reminding you about how `wc` works:
~~~
Returns the file with the most number
of lines in the list provided
To execute:
print-larger.sh <list-of-files>
wc: illegal option -- -
usage: wc [-clmw] [file ...]
~~~
{: .output}

> ## Other conditional structures.
> Here you have seen how to use Bash basic *if...then...else...fi*. However, there are other possible constructs that can be beneficial depending on the case under consideration:
> - **Else If ladder**. This structure is build as *if..elif..else..fi* and allows you to select one of many blocks of code to execute. It will test *expression1* and if it is true execute the corresponding set of commands, if *expression1* is false, it checks *expression2*, and if all the expressions are false, then it enters into the *else* block and executes the corresponding commands.
> ~~~
> If [[ conditional expression1 ]]
> then
> 	commands...
> elif [[ conditional expression2 ]]
> then
> 	commands...
> elif [[ conditional expression3 ]]
> then
> 	commands...
> else
> 	commands...
> fi
> ~~~
> {: .language-bash}
{: .callout}

## File Test Operators
In addition to variable comparisons, there are other comparison operators that can be used to query the existence and attributes of files. This would allow the script author to, for example, test whether a file exists before trying to read it and potentially producing an error. The table below summarizes some of these operators:

| **Operator** *ARGUMENT* | Purpose                           |
|-------------------------|-----------------------------------|
| **-d** *DIRECTORY*      | Test for existence of a directory |
| **-f** *FILENAME*       | Test for existence of file        |
| **-r** *FILENAME*       | Test if file is readable          |
| **-w** *FILENAME*       | Test if file is writable          |
| **-x** *FILENAME*       | Test if file is executable        |

It is also possible to check for the negative outcome of a test by preceeding the statement with a **!** symbol. For example:
~~~
if [[ ! -f myfile.txt ]]; then
  echo "File does not exist"
fi
~~~
{: .language-bash}


> ## Exercise
> This code shows an example of using these file test operators to check for the existence of a file before trying to display its contents. If a file test was not initially made and the file did not exist then the script would exit with an error. Checking for its existence first allows the case of its potential absence to be handled sympathetically rather than failing unexpectedly. Note that this script also includes some examples of including standard shell commands like cat and touch to display a file and create an error log file (which could in turn be tested later on respectively).
> ~~~
>  #!/bin/bash
>  # Write some text to a file
>  echo "Hello there" >> filetest.txt
>  
>  #If the file exists then print the text and delete the file,
>  #otherwise create an error file
>  if [[ -f filetest.txt ]]; then
>      echo "File found:"
>      cat filetest.txt
>      rm filetest.txt
>  else
>      echo "File: filetest.txt doesn’t exist"
>      touch file_err.log
>  fi
> ~~~  
> {: .language-bash}
> Currently *file_err.log* is never created since the *filetest.txt* file is always written to at the very start of the script. What happens when the line writing *Hello there* to *filetest.txt* is commented out? Try writing the equivalent for a directory test, or query the executable state of a file.
>
> > ## Solution
> > ~~~
> > #!/bin/bash
> > # Write some text to a file
> > echo "Hello there" >> filetest.txt
> > 
> > #If the file exists then print the text and delete the file,
> > #otherwise create an error file
> > if [[ -f filetest.txt ]]; then
> >   echo "File found:"
> >   cat filetest.txt
> >   rm filetest.txt
> > else
> >   echo "File: filetest.txt doesn’t exist"
> >     touch file_err.log
> > fi
> > 
> > mkdir dir_script_test
> > if [[ -d dir_script_test ]]; then
> >   echo "Dir found: dir_script_test"
> >   rmdir dir_script_test
> > else
> >   echo "Dir dir_script_test doesn't exist"
> >   touch dir_err.log
> > fi
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}
