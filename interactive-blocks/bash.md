---
cover: ../.gitbook/assets/image (7).png
coverY: 0
---

# Bash

From Ahmed Farag

To write bash code start with `#!/bin/bash` → because system show this file as ASCII text

`echo` or `printf` we use it for printing

**BASH** → use interpreter bash (print correct line then print the error is is found)

***

***

### implementation

to implement variable

```bash
#!/bin/bash
# no spaces before and after equal -> before space execute as a command 

name="irix"
echo $name  # to print the value for variable
printf $name
```

the difference between `echo`,`printf` :

* **printf** → print text in the same line
* **echo** → print text in new line (to print text in the same line we use option -n )

To save the value for execution command we use ``-> `var=`ls``

### mathematical operation

*   **$(())**

    ```bash
    num1=5;num2=4
    echo $(($num1+$num2)) # sum num1,num2 the print the value for summation
    ```

    with all operation except postfix and prefix ((x++))
*   **let**

    ```bash
    let x=1+10  
    echo $x  # output is 11
    ```
*   **expr**

    ```bash
    # take care with space in expr
    x= expr 1 + 10   # same spaces
    echo $x
    ```

***

***

### input

to take input from user we use

```bash
# first way
echo "enter your name"
read name      # read use to read input from user and stored it in variable

# second way
num1=$1  # input in var1
num2=$2  # input in var2
# ./calc.sh 10 5  
```

**read** → we have more option for it like :

* -s —> make input non visible (like password in linux) `read -s $name`
* -p —> print text (like input in python) `read -p "what is your name: " $name`
*   array

    we use `set` to implement array

    ```bash
    set 10 20 30 50 90
    echo $(($1+$5))  # the value is 100
    set what is your name
    echo $1  # the output is what
    ```

    implement array , we use `${}` to print array

    ```bash
    os=('linux' 'mac' 'windows' 'android')  # WE USE () TO implemant array
    echo ${os[0]}  # output linux

    # to print all element in the array we use *
    echo ${os[*]}

    #remove element from array we use
    unset os[1]

    #insert value 
    os[2]='hello'
    ```
*   connection

    no `concatenation` in bash

    ```bash
    set 10 20 30 50 90
    echo $1 $2 $3   # output is 10 20 30
    ```

### execution

to execute command on system `$()`

```bash
var=$(whoami)
# or
var=`whoami`
echo $var 

echo $(whoami)
#or
echo $WHOAMI
```

***

***

### condition

*   **if** condition

    ```bash
    if [ $num1 == $num2 ] # must space between []
    then
    	echo "yes"
    elif [ $num1>$num2 ]
    then
    	echo "keep"
    else
    	echo "no"
    fi  # to end condition
    ```

    The **-f** checked if the file existed.

    The **-w** checked if the file was writable, without write permissions we wouldn't be able to output our text into the file.

    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/90e49eb5-9520-43b0-a51f-ec506194b09d/acdbb767-559d-4c3a-998e-535e9439a952/image.png)
*   **Test** condition

    ```bash
    test 001==1 && echo "True" || echo "False"   # output is false (because it compare two string not two integer) To compare integer use -eq,-ge,etc..
    ```

    just check on two variable like `?` in cpp

***

***

### loop

`echo {1..6}` → regular expiration (out put from 1 to 6)

*   **For** loop

    * for $variable\_name in 1 2 3 4
    * do
    * expiration
    * done

    ```bash
    seqq="hello world"
    for i in $seqq
    do
    echo -n $i "   "
    done

    #output -> hello    world  
    ```
*   **While** loop

    * while \[condition]
    * do
    * expiration
    * done

    ```bash
    $i=0
    while [true]
    do
    echo $i
    i=$((i+1))
    done
    ```

To Debug your code we use :

```bash
set -x
code here
set +x

# show all operation step by step
```

***

***

### String

compare two strings

```bash
if [ $str1=$str2 ]
then
echo "print"
elif [ $str1\\<$str2 ]  # greeter than \\< , less than >\\
```

To count chars in word

```bash
x="hello world"

length=`echo $x | wc -c`  # -c is option refer to chars
echo $length
```

**Split → IFS**

```bash
x="what is the name of the index file"

IFS=" "  # setting space as a delimiter 
read -ra arr <<< "$x"  # reading str as an array as token sepeated by IFS

echo ${arr[0]}  # output -> what
```

Substring `${var:start:end}`

```bash
x="what is the name of the index file"

substr=${x:0:10}
echo $substr
```

***

***

### Function

`function_name(){commands;}`

**or**

function\_name(){

commands

}

```bash
name(){
	echo hello world
}
name # call function
```

### Notes

exit 0 → to exit from code
