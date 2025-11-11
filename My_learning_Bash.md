# Bash

## Make script executable

```bash
touch script.sh     # create your script
chmod 755 script.sh # give exec grants
```

## Inside of sh file

`#!/bin/bash` means `#!/path/to/interpreter`

## Echo command

```bash
#!/bin/bash

echo "Scripting is fun!"  # prints a string to a console

# ./script.sh
# >>>Scripting is fun!
```

## Variables

```bash
# Assigning int, str or an array to a variable
# Rule 1. NO SPACES AROUND =
# Rule 2. Variable names in shell should be in UPPERCASE
NUM_1=5                         # int
NUM_2=3                         # int
VARIABLE_NAME="cat"             # str
MY_ARRAY=(apple banana cherry)  # array

# Ok
echo "This is $VARIABLE_NAME"
echo "This is also ${VARIABLE_NAME}"    # curly braces are optional here
echo "This is ${VARIABLE_NAME}astrophe" # will concat to 'This is catastrophe'
echo "${MY_ARRAY[0]}"                   # apple
echo "${MY_ARRAY[@]}"                   # All elements of the array

# Error
echo "This is $VARIABLE_NAMEastrophe"  # variable can't be found, must use { }

# Assign command's output to a variable
# Modern way
SERVER_NAME=$(hostname) 
SUM=$((NUM_1 + NUM_2))  # double parentheses
echo "You are running this script on a ${SERVER_NAME}"
echo "The sum is $SUM"

# Old way
SERVER_NAME=`hostname`  
echo "You are running this script on a ${SERVER_NAME}"
```

## Conditional expressions and If statements

Syntax  

```bash
if [ conditional-expression-to-test ]; then
    # Commands if condition is true
elif [ another-condition ]; then
    # Commands if another condition is true
else
    # Commands if none of the conditions are true
fi
```

You can check for:

- if strings are equal
- if a number is greater than another
- if a file exists

```bash
# File tests
[ -d FILE ]  # check if file is a directory
[ -e FILE ]  # check if file exists
[ -s FILE ]  # check if file exists and it is not empty
[ -f FILE ]  # check if file exists and it is a regular file
[ -r FILE ]  # check if file is readable by you
[ -w FILE ]  # check if file is writable by you
[ -x FILE ]  # check if file is executable by you
# Example
[ -e /etc/passwd ]  # will return True (status code 0) or False (status code 1)
[ -e "$MY_FILE" ]

# String tests
[ -z STRING ]  # check if string is empty
[ -n STRING ]  # check if string is not empty
[ STRING_1 = STRING_2 ]  # check if strings are equal
[ STRING_1 != STRING_2 ]  # check if strings are not equal
# Example
[ "$MY_STRING" = "hello" ]

# Arithmetic test for numbers
[ X -eq Y ]  # (==) check if X is equal to Y
[ X -ne Y ]  # (!=) check if X is not equal to Y
[ X -lt Y ]  # (<)  check if X is less than Y
[ X -le Y ]  # (<=) check if X is less than or equal to Y
[ X -gt Y ]  # (>)  check if X is greater than Y
[ X -ge Y ]  # (>=) check if X is greater than or equal to Y
# Example
[ $MY_NUM -gt 5 ]

# Alternate syntax
[[ $MY_STRING = hello ]]  # You don't need to escape the strings with "" signs
[[ $MY_STRING == h* ]]    # You can use wildcards with * and ? and regular exp =

# Example of an IF statement
MY_SHELL="csh"
if [ "$MY_SHELL" = "bash"]; then 
    echo "You seem to like tha bash shell."
elif [ "$MY_SHELL" = "csh"]; then 
    echo "You seem to like tha csh shell."
else
    echo "You don't seem to like tha bash or csh shells."
fi
```

## For loop

Syntax:

```bash
for VAR in LIST; do
    # Commands to execute for each item in the list
done

```

`VAR` this variable takes the value of each item in the list.  
`LIST` the list of items to iterate through.  

Iteration through a list of strings:

```bash
for COLOR in red green blue; do
    echo "The color is $COLOR now"
done

# or

COLORS="red green blue"
for COLOR in $COLORS; do
    echo "The color is $COLOR now"
done

# Result:
# The color is red now
# The color is green now
# The color is blue now
```

Iteration through number range:

```bash
for NUM in {1..3}; do
    echo "Number is $NUM"
done

# Result
# Number is 1
# Number is 2
# Number is 3


# Using a step
for NUM in {1..10..2}; do
    echo "Number is $NUM"
done

# Result
# Number is 1
# Number is 3
# Number is 5
# Number is 7
# Number is 9
```

Iteration through an array:

```bash
FRUITS={"apple" "banana" "cherry"}
for FRUIT in "${FRUITS[@]}"; do
    echo "Fruit is $FRUIT"
done

# Result
# Fruit is apple
# Fruit is banana
# Fruit is cherry
```

Iteration through files in a dir:

```bash
for FILE in /path/to/directory/*; do
    echo "File is $FILE"
done

# Result
# Prints every file in /path/to/directory/
```

Using a command's result for iteration:

```bash
for USER in $(who | awk '{print $1}'); do
    echo "Logged in user: $USER"
done

# Result
# $(who | awk '{print $1}') -> gets a list of users which are logged in, then each username will be printed
```

Nested for loops:

```bash
for i in {1..3}; do
    for j in {a..c}; do
        echo "Combination is $i-$j"
    done
done

# Result
# Combination is 1-a
# Combination is 1-b
# Combination is 1-c
# Combination is 2-a
# Combination is 2-b
# ...
```

Control of the iteration:

```bash
for num in {1..5}; do
    if [ $num -eq 3 ]; then
        echo "Skipping 3"
        continue
    fi
    
    if [ $num -eq 5 ]; then
        echo "Breaking at 5"
        break
    fi

    echo "Number is $num"
done

# Result
# Number is 1
# Number is 2
# Skipping 3
# Number is 4
# Breaking at 5
```

One-liners:

```bash
for i in {1..5}; do echo "Hello world $i"; done
```

Other useful scripts with FOR loop

```bash
# This script adds (renames) current date to file names for JPG files
PICTURES=$(ls *jpg)
DATE=$(date +%F)  # The output is like this 2023-08-01

for PICTURE in $PICTURES; do
    echo "Renaming ${PICTURE} to ${DATE}-${PICTURE}"
    mv ${PICTURE} ${DATE}-${PICTURE}
done

# if you have this files: 
    # bear.jpg
    # man.jpg
    # pig.jpg
# They will be renamed into:
    # 2023-08-01-bear.jpg
    # 2023-08-01-man.jpg
    # 2023-08-01-pig.jpg
```

## Positional parameters

```bash
#!/bin/bash

# These parameters will be stored in special variables as well as the script's name
# $ my_script.sh parameter_1 parameter_2 parameter_3
# $0 : "my_script.sh"
# $1 : "parameter_1"
# $2 : "parameter_2"
# $3 : "parameter_3"
# ... till $9 ???

# This script called archive_user.sh accepts a parameter which happens to be a username

echo "Executing script: $0"
echo "Archiving user: $1"
passwd -l $1  # locks the account
tar cf /archives/${1}.tar.gz /home/${1}  # creates an archive of the home directory

# $ ./archive_user.sh elvis
# Executing script: ./archive_user.sh
# Archiving user: elvis
# passwd: password expiry information changed.
# tar: Removing leading `/' from member names
# $

USER=$1
echo "Executing script: $0"
echo "Archiving user: $USER"
passwd -l $USER  # locks the account
tar cf /archives/${USER}.tar.gz /home/${USER}  # creates an archive of the home directory

# We can access all the positional parameters starting at $1 to the last one on the command line
echo "Executing script: $0"
for USER in $@
do
    echo "Archiving user: $USER"
    passwd -l $USER
    tar cf /archives/${USER}.tar.gz /home/${USER}
done

# $ ./archive_user.sh chet joe
# Executing script: ./archive_user.sh
# Archiving user: chet
# passwd: password expiry information changed.
# tar: Removing leading `/' from member names
# Archiving user: joe
# passwd: password expiry information changed.
# tar: Removing leading `/' from member names
# $
```

## User input stdin

```bash
#!/bin/bash

# The read command accepts STDIN
# Syntax
# read -p "PROMPT" VARIABLE

# STDIN comes from:
    # typing from a keyboard
    # output of a command (command pipeline)

read -p "Enter a user name: " USER
echo "Archiving user: $USER"
passwd -l $USER  # locks the account
tar cf /archives/${USER}.tar.gz /home/${USER}  # creates an archive of the home directory

# $ ./archive_user.sh
# Enter a user name: mitch
# Archiving user: mitch
# passwd: password expiry information changed.
# tar: Removing leading `/' from member names
# $
```

## Exit status

```bash
#!/bin/bash

# every script return EXIT STATUS or CODE
exit 0  # means SUCCESS
exit 1  # ERROR
...
exit 255  # ERROR

# $? this variable contains last command exit status

HOST="google.com"
ping -c 1 $HOST

if [ "$?" -eq "0" ]
then
    echo "$HOST reachable."
else
    echo "$HOST unreachable."
fi

HOST="google.com"
ping -c 1 $HOST
RETURN_CODE=$?

if [ "$RETURN_CODE" -ne "0" ]
then
    echo "$HOST unreachable."
fi

HOST="google.com"
ping -c 1 $HOST

if [ "$?" -ne "0" ]
then
    echo "$HOST unreachable."
    exit 1
fi
exit 0

```

## Logical operators

```bash
#!/bin/bash

# Syntax
# command_1 && command_2
# command_2 will be executed only if command_1 was successful
mkdir /tmp/bak && cp test.txt /tmp/bak
HOST="google.com"
ping -c 1 $HOST && echo "$HOST reachable."

# Syntax
# command_1 || command_2
# command_2 will be executed only if command_1 failed
cp test.txt /tmp/bak || cp test.txt /tmp
HOST="google.com"
ping -c 1 $HOST || echo "$HOST unreachable."

# Syntax
# command_1 ; command_2
# All commands will be executed no matter what exit status of the previous command
cp test.txt /tmp/bak ; cp test.txt /tmp
# Same ass
cp test.txt /tmp/bak
cp test.txt /tmp

```

## Functions

```bash
#!/bin/bash

# Syntax
# Create a function approach #1
# function my-function() {
#     echo "hello"
# }

# Create a function approach #2
# my-function() {
#     echo "hello"
# }
# To call function type it's name without ()
# my-function

# Functions parameters are stored in $1, $2 and etc.
# $@ contains all the parameters
# $0 is NOT the name of the function, it's the script's name

function hello() {
    echo "Hello $1"
}
hello Jason
# output is
# Hello Jason

function hello() {
    for NAME in $@
    do
        echo "Hello $NAME"
    done
}
hello Jason Dan Ryan

# Variable scope
GLOBAL_VAR=1
# GLOBAL_VAR is available in the function
my_function

# GLOBAL_VAR is NOT available in the function
my_function
GLOBAL_VAR=2
# Put global vars in the beginning of the script

# To create a local variable inside the function use keyword LOCAL
# Only functions can have local variables
# The best practice is to keep variables local in the functions
local MY_LOCAL_VAR=1

```

## Return exit statuses

```bash
#!/bin/bash

# Syntax
# Functions returns statuses as scripts
# If no explicit status was specified, then the status of the last command will be used.

function backup_file() {
    if [ -f $1 ]
    then
        # $$ is process id of the script. WIll be unique every time you run the script
        BACK="/tmp/$(basenode ${1}).$(date +%F).$$"
        echo "Backing up $1 to ${BACK}"
        cp $1 $BACK
    else
        return 1
    fi
}

backup_file etc/hosts

if [ $? -eq 0]
then
    echo "Backup succeeded!"
else
    echo "Backup failed!"
    exit 1
fi
```

## Wildcards

```bash
#!/bin/bash

# Syntax
# A string or a pattern matching to get list of files or directories
# * to match zero or more
# ? to match exactly one
# [] to match specific characters in between of brackets
# [!] to match all character NOT included in between of brackets
# [a-g] to create a range from a to g including
# [3-6] to create a range from 3 to 6 including
# pre-defined ranges
# [[:alpha:]] to match alphabetic both lower and upper case letters
# [[:digit:]] to match digits from 0 - 9
# [[:alnum:]] to match alpha + digits
# [[:upper:]] to match UPPER case letters
# [[:lower:]] to match lower case letters
# [[:space:]] to match all space like whitespaces, tabs
# \ to escape special characters 
```

## Case statement

```bash
#!/bin/bash

#!/bin/bash

# Syntax
read -p "Enter y or n: " ANSWER

case "$ANSWER" in
    [yY]|[yY][eE][sS])
        echo "You answered yes."
        ;;
    [nN]|[nN][oO])
        echo "You answered no."
        ;;
    *)
        echo "Invalid answer."
        ;;
esac
```

## Logging

```bash
#!/bin/bash

# Syntax
# Usually we are logging who, what, when, where, why
# The syslog standard uses facilities and severities to categorize messages
# Facilities:
#   kern
#   user
#   mail
#   daemon
#   auth
#   local0
#   local7
# Severities:
#   emerg
#   alert
#   crit
#   err
#   warning
#   notice
#   info
#   debug
# Log file locations are configurable:
#   /var/log/message
#   /var/log/syslog

# add message that you want to log
logger "Message"
# result
# Aug 2 01:22:34 linuxserver user_1: Message

# add facility and severity
# logger -p facility.severity "Message"
logger -p local0.info "Message"
# result
# Aug 2 01:22:34 linuxserver user_1: Message

# to add tag, usually your script name
logger -t myscript -p local0.info "Message"
# result
# Aug 2 01:22:34 linuxserver user_1: Message

# to add PID
logger -i -t myscript "Message"
# result
# Aug 2 01:22:34 linuxserver user_1: Message

log_it() {
    local LOG_LEVEL=$1
    shift  # to shift the positional parameters to the left? So $1 is gone now
    MSG=$@
    TIMESTAMP=$(date +"%Y-%m-%d %T")
    if [ $LOG_LEVEL = "ERROR" ] || $VERBOSE
    then
        echo "${TIMESTAMP} ${HOST} ${PROGRAM_NAME} [${PID}]: ${LOG_LEVEL} ${MSG}"
    fi
}
```

## While loop

```bash
#!/bin/bash

# Syntax
# while [ condition-is-true ]
# do
#     command_1
#     command_2
#     command_N
# done

# infinite loops
# while true
# do
#     command_N
#     sleep 5  # sleeps 5 for seconds
# done

# will run 5 times
INDEX=5
while [ $INDEX -lt 6 ]
do
    echo "Creating project-${INDEX}"
    mkdir /usr/local/project-${INDEX}
    ((INDEX++))  # equivalent to i++
done
# result
# Creating project-1
# Creating project-2
# Creating project-3
# Creating project-4
# Creating project-5

while [ "$CORRECT" != "y" ]
do
    read -p "Enter your name: " NAME
    read -p "Is ${NAME} correct? " CORRECT
done

while ping -c 1 app1 > /deb/null
do
    echo "app1 still up..."
    sleep 5
done

echo "app1 down, continuing"

LINE_NUM=1
while read LINE
do
    echo "${LINE_NUM}: ${LINE}"
    ((LINE_NUM++))
done < /etc/fstab

grep xfs /etc/fstab | while read LINE
do
    echo "xfs: ${LINE}"
done

FS_NUM=1
grep xfs /etc/fstab | while read FS MP REST
do
    echo "${FS_NUM}: file system: ${FS}"
    echo "${FS_NUM}: mount point: ${MP}"
    ((FS_NUM++))
done

for LINE in $FILE_NAME
do
    echo $LINE
done
```

## Debugging

```bash
#!/bin/bash

# -x Prints commands as they execute
# +x Stop printing commands as they execute (default mode)
# -e Exits script on error
# -v Prints shell input lines as they are read

# you can combine options #!/bin/bash -xve

#!/bin/bash -x
# or
set -x

#!/bin/bash +x
# or
set +x

# Example 1:
#!/bin/bash -x
TEST_VAR="test"
echo "$TEST_VAR"

# Output 1:
# + TEST_VAR=test
# + echo test
# test

# Example 2:
#!/bin/bash -x
TEST_VAR="test"
set -x
echo "$TEST_VAR"
set +x
hostname

# Output 1:
# + echo test
# test
# + set +x
# linuxhostname

# -x uses PS4 variable, by default it's + sign
# PS4 could be reassigned like PS4='+ $BASH_SOURCE : $LINENO '
```

## DOS vs Linux file types

```bash
# if you have carriage symbols from DOS files you need to process them with
# dos2unix
# or unix2dos
```

## SED

```bash
# sed
# sed = stream editor. Streams == pipes
# streams are typically are textual data
# Usages:
#    to Substitute some text for other text
#    to remove lines
#    to append text after given lines
#    to insert text before given lines
# sed is used programmatically, not interactively

# how it works
sed 's/search-pattern/replacement-string/flags' target_file.txt

# Example 1 - basic replacement
echo 'Dwight is the assistant regional manager' > manager.txt
sed 's/assistant/assistant to the/' manager.txt

# Example 2 - case insensitive replacement
echo 'I love my wife.' > love.txt
sed 's/my WIFE/sed/i' love.txt

# Example 3 - will replace only the first appearance in the line
echo 'This is line 2.' >> love.txt
echo 'I love my wife with all of my heart.' >> love.txt
sed 's/my wife/sed/' love.txt

# Example 4 - add flag g=global to replace all the appearances
sed 's/my wife/sed/g' love.txt

# Example 5 - to replace only second appearance, so the first one will be skipped
sed 's/my wife/sed/2' love.txt

# Example 6 - saving the result in a new file. Original one will remain unaltered.
sed 's/my wife/sed/g' love.txt > my-new-love.txt

# Example 7 - saving the result in the original file, but creating a backup file first.
# IMPORTANT no space after -i
sed -i.bak 's/my wife/sed/g' love.txt

# Example 8 - will save ONLY affected rows in a new file
sed 's/love/like/gw like.txt' love.txt

# Example 9 - you can give a file to sed through pipes
cat love.txt | sed 's/my wife/sed/g'

# Example 10 - escaping / in the text
echo '/home/jason' | sed 's/\/home\/jason/\/export\/users\/jason/'

# Example 11 - escaping / in the text with custom delimiters
echo '/home/jason' | sed 's#/home/jason#/export/users/jason#'
echo '/home/jason' | sed 's:/home/jason:/export/users/jason:'

# Example 12 - delete rows with a flag d=deletion
sed '/This/d' love.txt

# Example 13 - multiple sed's in one line
# /^#/d - deletes rows starting with #
# /^$/d - deletes empty rows
sed '/^#/d ; /^$/d ; s/apache/httpd' config.txt
sed -e '/^#/d' -e '/^$/d' -e 's/apache/httpd' config.txt

# Example 14 - read sed commands from a file
echo '/^#/d' > script.sed
echo '/^$/d' >> script.sed
echo 's/apache/httpd' >> script.sed
sed -f script.sed config.txt

# Example 15 - use addresses to narrow the area of replacement, for example to search only in line 2
sed '2 s/apache/httpd' config.txt
sed '2s/apache/httpd' config.txt # you can omit the space after 2

# Example 16 - use regex in addresses, will change only in lines with Group
sed '/Group/ s/apache/httpd' config.txt

# Example 17 - use range of lines to work with, like only 1, 2 and 3 line
sed '1,3 s/run/execute/' config.txt

# Example 18 - use regex to define a range of lines
sed '/#User/,/^$/ s/run/execute/' config.txt
```
