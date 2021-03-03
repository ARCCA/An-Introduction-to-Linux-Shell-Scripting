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

A common practical application of `if` statements in programming scripts is to add 
a `help` flag to print some useful information about the script. For example:
~~~
if [[ "$1" == "--help" ]]
    then
    echo "Returns the file with the most number"
    echo "of lines in the list provided"
    echo "To execute:"
    echo "print-largest.sh <list-of-files>"
fi

wc -l $@ | sort -n | tail -1
~~~
{: .language-bash}

The above script **should** print the help message when executed like this:
~~~
bash print-largest.sh --help
~~~
{: .source}

But you might see an addional error message reminding you about how `wc` works:
~~~
Returns the file with the most number
of lines in the list provided
To execute:
print-largest.sh <list-of-files>
wc: illegal option -- -
usage: wc [-clmw] [file ...]
~~~
{: .output}

What is happening in this case is that `bash` is evaluating the conditional test in 
our `if` statement and executing the commands if the condition is true, however, our
script doesn't end there and `bash` continues executing any commands after the `if` 
statement. If for some reason we would like to exit our script at some point (e.g. in
case some condition is not satisfied) we can use the command `exit` to instruct 
`bash` to quit the script at that point. `exit` takes a numeric argument that is used
to specify what caused the program to exit (convention is to use `0` if everything is
ok, and is the numeric value used by your script if it finishes without errors, try
using `$?` after executing your script to take a look at this value). We can tell
`bash` to exit our script after printing our help message in this way:
~~~
if [[ "$1" == "--help" ]]
    then
    echo "Returns the file with the most number"
    echo "of lines in the list provided"
    echo "To execute:"
    echo "print-largest.sh <list-of-files>"
    exit 0
fi

wc -l $@ | sort -n | tail -1
~~~
{: .language-bash}

~~~
bash print-largest.sh --help
~~~
{: .source}

~~~
Returns the file with the most number
of lines in the list provided
To execute:
print-largest.sh <list-of-files>
~~~
{: .output}

This time the script exits inmediately after printing the help message without trying
to execute the `wc` command.

## Counting arguments with `$#`

One further `bash` operator useful when working with scripts that take external 
arguments is to use the `$#` operator. For example, try the following script (save to
*counting-arguments.sh*):
~~~
echo the number of arguments received is $#
~~~
{: .language-bash}
And run like this (try with a different number of arguments).
~~~
bash counting-arguments.sh argument1 argument2 argument3
~~~
{: .language-bash}
~~~
the number of arguments received is 3
~~~
{: .output}

We can use `$#` to make our help printing `if` statement even more flexible. For 
example, imagine a new researcher just received our script and has no idea what to do
with it he or she migh be tempted to run the script like this and see what happens:
~~~
bash print-largest.sh
~~~
{: .language-bash}
But this would led to the script hangging! (you can cancel it with 
<kbd>Ctrl</kbd>+<kbd>C</kbd>). A more useful default behaviour would be that if your
script requires arguments to work, trying to run it with no arguments cause the help
message to be printed. We can do this with `$#` like this:
~~~
if [[ "$1" == "--help" ]] || [[ $# -eq 0 ]]
    then
    echo "Returns the file with the most number"
    echo "of lines in the list provided"
    echo "To execute:"
    echo "print-largest.sh <list-of-files>"
    exit 0
fi

wc -l $@ | sort -n | tail -1
~~~
{: .language-bash}

~~~
bash print-largest.sh
~~~
{: .source}

~~~
Returns the file with the most number
of lines in the list provided
To execute:
print-largest.sh <list-of-files>
~~~
{: .output}

That's a more useful default behaviour!. Notice the `if` structure we have used where
we have included a new operator `||` (that is two vertical lines, look for the key 
<kbd>|</kbd> in your keyboard) that works as a logical `OR` (there is an equivalent 
`&&` operator that works as a logical `AND`) and let us two or more comparison tests.

> ## Other conditional structures.
> Here you have seen how to use `bash` basic *if...then...else...fi* and the Else If 
> ladder structure. However, there are other `bash` constructs that could be useful
> depending on the case under consideration:
> - **Case Statements**. Is a more slightly more complex conditional structure useful
> when we have several posible options. The general structure is:
>
> ~~~
> case EXPRESSION in
>   CASE1)
>     COMMAND-LIST;;
>   CASE2)
>     COMMAND-LIST;;
>   CASEN) 
>     COMMAND-LIST;;
>   * )
>     COMMAND-LIST;;
> esac
> ~~~
> {: .language-bash}
{: .callout}

## File Test Operators
In addition to variable comparisons, there are other comparison operators that can be
used to query the existence and attributes of files. This would allow the script 
author to, for example, test whether a file exists before trying to read it and
potentially producing an error. The table below summarizes some of these operators:

| **Operator** *ARGUMENT* | Purpose                           |
|-------------------------|-----------------------------------|
| **-d** *DIRECTORY*      | Test for existence of a directory |
| **-f** *FILENAME*       | Test for existence of file        |
| **-r** *FILENAME*       | Test if file is readable          |
| **-w** *FILENAME*       | Test if file is writable          |
| **-x** *FILENAME*       | Test if file is executable        |

For example, to test if the directory *molecules* exists:
~~~
$  if [ -e molecules ]
> then
>     echo moecules exists
> fi
~~~
{: .language-bash}

Another common task is to identify directories in a certain location. Try typing the
following for loop in a script called *search-directories.sh* and run it in our 
*data-shell-scripting* directory:
~~~
for filename in $@
do
    if [[ -d $filename ]]
    then
        echo $filename is a directory
    fi
done
~~~
{: .language-bash}

It is also possible to check for the negative outcome of a test by preceeding the
statement with a **!** symbol. For example:
~~~
if [[ ! -f myfile.txt ]]; then
  echo "File does not exist"
fi
~~~
{: .language-bash}

> ## Logging a directory's content
> Try modifying the above script to create a log file within an identified 
> directory. The log file should contain the names of the files inside the 
> directory. Avoid rewriting the log file if it already exists.
> > ## Solution
> > ~~~ 
> > for filename in $@
> > do
> >     if [[ -d $filename ]]
> >     then
> >        echo $filename is a directory
> >        if [[ ! -f $filename/${filename}.log ]]
> >        then
> >            echo creating ${filename}.log
> >            cd $filename
> >            ls > ${filename}.log
> >            cd ..
> >        else
> >            echo "Warning: ${filename}.log is already present!"
> >        fi
> >     fi
> > done
> > ~~~
> > {: .language-bash}
> > The above script shows an example of using a few file test operators to check for
> > the existence of a directories and files before trying to perform an action
> > (creating a log file). If the file test were not performed and the file already 
> > existed we would rewrite potentially valuable data. Checking for its existence 
> > first allows us to throw a warning in this case and perhaps performing another 
> > action (e.g. backing up the log file already present).
> {: .solution}
{: .challenge}
