---
title: "Arithmetic and Arrays"
teaching: 30
exercises: 10
questions:
- How to perform basic arithmetic operations in Bash?
- How to define arrays?
objectives:
- "Understand Bash variables types and how to construct arithmetic operations."
- "Understant how to construct Bash arrays and access their contents."
keypoints:
- "Bash is an untyped language. This means that all variables are stored as strings."
- "The **$((       ))** construct is used to create arithmetic operations"
- "Bash arrays are created byi enclosing the elements within a pair of brackets, **(  )**"
- "We can find the size of a Bash array with the construct **${#ArrayName[@]}**"
---

## **Bash** Variables
Before going into detail about how to perform arithmetic operations and define arrays
in **Bash**, it would be useful to do a quick recap of how variables are used in 
**Bash**. As in other languages, **Bash** variables allow the script's author to
refer to data by a label. In **Bash** this assignment is performed using the `=`
symbol: We can display this using the command line using the export command to set
the variable.
<pre style="color: silver; background: black;">
$ export MYTEXT=‘Hello’
</pre>

The contents of a variable can then be obtained by a process called dereferencing or 
variable substitution. To return the value assigned to a variable, prefix the label
using the $ symbol, i.e:
<pre style="color: silver; background: black;">
$ echo $MYTEXT
Hello
</pre>

> ## More on **Bash** variables
> If you are familiar with a language like C++ or Fortran, where variables have
> explicit types such as integers or characters, you may be surprised that the
> contents of a **Bash** variable may be assigned with no declaration or preamble.
> This is because **Bash** is what is known as an untyped language. Essentially, all
> variables are stored as strings and the contents is treated differently depending
> on context. So, for example, if I try and print the contents of a variable as above
> using the `echo` command, **Bash** treats it like a text string. If I try and
> multiply it by 2 then **Bash** will treat it as a number.
> 
> Needless to say, whilst this can simplify the process of creating and assigning
> variables, it requires the author to be more careful in how variables are treated, 
> as multiplying a string by 2 will not produce an error as it would when using a
> strongly typed language.
{: .callout} 

## Arithmetic Expansion
Now we know that **Bash** treats variables according to context, let's see how we can
perform arithmetic operations whereby **Bash** regards the contents as a number. The
format for the **Bash** arithmetic expansion is:
~~~
$(( arithmetic expression ))
~~~
{: .language-bash}

For example:
<pre style="color: silver; background: black;">
$ echo $((1 + 3))
4
</pre>

Arithmetic operations are surrounded by the **$((...))** construct. Notice that the 
result of this expression can be again stored in a variable.
<pre style="color: silver; background: black;">
$ MYVAR=$((1 + 3))
$ echo $MYVAR
4
</pre>

Without this construct **Bash** would treat `1 + 3` as a string and print it
accordingly:
<pre style="color: silver; background: black;">
$ echo 1 + 3
1 + 3
</pre>

Consider the following script. Here we define two variables *X* and *Y* with integer 
values and proceed to use the construct **$((...))** so that **Bash** understands 
that we want to treat the variables as numbers and wish to perform some simple 
mathematical operations. We store the result of these operations in a variable 
*RESULT* and use it in further arithmetic operations.
~~~
#!/bin/bash
# Assign two variables with integer values.
X=7
Y=12

# Add these two variables together, store the result in a third variable,
# and print out the result
RESULT=$((X+Y))
echo "X + Y = ${RESULT}!"

# Increment the result by 5.
RESULT=$((RESULT+5))
echo "RESULT is now ${RESULT}"

# Divide the result by 5. Remember bash only deals with integers.
DIVISION=$((RESULT/5))
echo "${RESULT} divided by 5 is ${DIVISION}"
~~~
{: .language-bash}

Running this script should produce the following result:
<pre style="color: silver; background: black;">
X + Y = 19!
RESULT is now 24
24 divided by 5 is 4
</pre>

> ## Exercise
> Now try modifying the previous script to perform some other calculations. 
> How would you expect to be able to multiply two numbers, or take one number away
> from another?
>
> > ## Solution
> > ~~~ 
> > #!/bin/bash
> > #Add these two variables together, store the result in a third variable,
> > #and print out the result
> > RESULT=$(($1+$2))
> > echo "$1 + $2 = ${RESULT}!"
> > 
> > #Increment the result by 5.
> > RESULT=$((RESULT+5))
> > echo "RESULT is now ${RESULT}"
> > 
> > #Divide the result by 5. Remember bash only deals with integers.
> > DIVISION=$((RESULT/5))
> > echo "${RESULT} divided by 5 is ${DIVISION}"
> > 
> > #Solution
> > MULTIPLY=$(($1 * $2))
> > echo ${MULTIPLY}
> > 
> > SUBTRACT=$(($1-$2))
> > echo ${SUBTRACT}
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge} 

## Counters
A useful **Bash** arithmetic feature is its capability to 
post/pre-increment/decrement variables similar to other languagues such as C++.
For example, try the following:
<pre style="color: silver; background: black;">
$ MYCOUNTER=0
$ echo $((++MYCOUNTER))
1
$ echo $MYCOUNTER
1
$ echo $((++MYCOUNTER))
2
$ echo $MYCOUNTER
2
</pre>

Now try:
<pre style="color: silver; background: black;">
$ MYCOUNTER=0
$ echo $((MYCOUNTER++))
0
$ echo $MYCOUNTER
1
$ echo $((MYCOUNTER++))
1
$ echo $MYCOUNTER
2
</pre>

In the first instance we use a pre-increment operator where the variable is
incremented by 1 before a command is executed (in this case `echo`). While in the 
second example we used a post-increment operator, where the operation/command is
executed first and then the variable is incremented. These operators can be
particularly useful when working with **Bash** C-styled `for` loops, for example:
<pre style="color: silver; background: black;">
$ for ((i = 0 ; i < 5 ; i++)); do
>   echo "my counter has a value of $i"
> done
my counter has a value of 0
my counter has a value of 1
my counter has a value of 2
my counter has a value of 3
my counter has a value of 4
</pre>

You can find a more comprehensive list of **Bash** arithmetic operators [here](https://www.gnu.org/software/bash/manual/html_node/Shell-Arithmetic.html).

## Arrays
Arrays allow the script author to associate a number of values with a single label. This means you only need to remember one variable name instead of many, which can come in particularly useful if you have potentially hundreds of values you want to store, such as the words in a text file you have just read in.

In Bash, arrays are one dimensional, zero indexed, and sparse, and much like variables they don't require formal declaration in order to use them.

| Array feature  | Description |
|----------------|-------------|
| Dimensionality | The number of indices used to locate an array element. For example, a two dimensional array could describe a set of rows and columns and have indexes i, j. |
| Indexing|The number of the first element in an array. For example an array of ten elements can be referred to as elements 0 to 9 or 1 to 10. The former is zero-based indexing and the latter is one-based indexing. |
| Layout|An array in which every possible element is defined is fully populated. One in which only certain elements, i.e. three out of ten, have values is sparse. |

For the most part you won't need to worry about any of this, but if you're familiar with language in which arrays are more tightly defined or have additional features, BASH arrays may seem quite simple and loosely defined.

Creating an array in Bash is as simple as enclosing the elements within a pair of brackets, **(  )**. We can display this using the command line to set the array. For example:

<pre style="color: silver; background: black;">
$ MY_ARRAY=(1 2 3 ‘Hawk’)
</pre>

This example demonstrates another potentially unexpected feature of Bash arrays: each of the elements can appear to be a different type. But remember, we learned that Bash is an untyped language, so this is a potentially useful consequence.

Accessing the contents of an array element can be achieved using the following construct:

<pre style="color: silver; background: black;">
$ echo ${MY_ARRAY[3]}
Hawk
</pre>

Finding the size of an array is very useful when using them as part of loops. This can be done with the construct **${#ArrayName[@]}**. For example:
<pre style="color: silver; background: black;">
$ echo ${#MY_ARRAY[@]}
</pre>


Here’s a script that shows some additional ways of accessing array contents.
~~~
#!/bin/bash
# Define a one dimensional array
MY_ARRAY=(1 2 3 ’raven’)

# Print out the contents of the third element
echo "The third element is ${MY_ARRAY[2]}."

# Return the number of elements in an array
echo "The array contains ${#MY_ARRAY[@]} elements"

# Print the contents of the entire array.
echo "The array consists of: ${MY_ARRAY[@]}"

# Define a sparse array and print out the contents
SPARSE_AR[0]=50
SPARSE_AR[3]='some words'
echo "Index 0 of spare array is ${SPARSE_AR[0]}."
echo "Index 1 of spare array is ${SPARSE_AR[1]}."
echo "Index 2 of spare array is ${SPARSE_AR[2]}."
echo "Index 3 of spare array is ${SPARSE_AR[3]}."
~~~
{: .language-bash}

That produces the following output:
<pre style="color: silver; background: black;">
The third element is 3.
The array contains 4 elements
The array consists of: 1 2 3 ’raven’
Index 0 of spare array is 50.
Index 1 of spare array is .
Index 2 of spare array is .
Index 3 of spare array is some words.
</pre>

Notice how despite the fourth element of the sparse array consists of two words, because we've enclosed them in a single block of quotes it is still considered a single element. We'll see in the section on Flow Control how we can use another form of array called a list to loop over blocks of commands to repeat operations without having to type out those commands multiple times.

> ## Exercise
> Consider the previous script. Try defining your own arrays and manipulating them. 
> Can you assign an array element to another variable or include the contents of a variable as an array element?
> > ## Solution
> > ~~~
> > #!/bin/bash
> > 
> > # Define a sparse array and print out the contents
> > SPARSE_AR[0]=50
> > SPARSE_AR[3]='some words'
> > 
> > VAR1=(${SPARSE_AR[0]})
> > echo "${VAR1}"
> > 
> > VAR2=77
> > 
> > SPARSE_AR[4]="${VAR2}"
> > echo ${SPARSE_AR[4]}
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}

