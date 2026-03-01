### Create Shell Variables

Shell variables are created by assigning a value to them using the  `=`  sign. Let's start by creating a simple shell script that defines some variables.
    
1.  Create a new file named  `variables.sh`  in the  `/home/labex/project`  directory:
    
    ```bash
    touch /home/labex/project/variables.sh
    ```
    
2.  Open the  `variables.sh`  file in the WebIDE and add the following content:
    
    ```bash
    #!/bin/bash
    
    PRICE_PER_APPLE=5
    MyFirstLetters=ABC
    greeting='Hello        world!'
    
    echo "Price per apple: $PRICE_PER_APPLE"
    echo "My first letters: $MyFirstLetters"
    echo "Greeting: $greeting"
    ```
    
    In this script, we've created three variables:
    
    -   `PRICE_PER_APPLE`: An integer variable
    -   `MyFirstLetters`: A string variable
    -   `greeting`: A string variable with multiple spaces
3.  Save the file.
    
4.  Make the script executable:
    
    ```bash
    chmod +x /home/labex/project/variables.sh
    ```
    
5.  Run the script:
    
    ```bash
    ./variables.sh
    ```
    
    You should see the following output:
    
    ```
    Price per apple: 5
    My first letters: ABC
    Greeting: Hello        world!
    ```
    
    Notice that the extra spaces in the  `greeting`  variable are preserved in the output when using single quotes to define the variable and not using quotes in the echo statement.

### Referencing Shell Variables

When referencing shell variables, there are a few scenarios where you need to use special syntax. Let's explore these cases.

1.  Open the  `variables.sh`  file in the WebIDE.
    
2.  Replace the content of the file with the following:
    
    ```bash
    #!/bin/bash
    
    PRICE_PER_APPLE=5
    MyFirstLetters=ABC
    greeting='Hello        world!'
    
    # Escaping special characters
    echo "The price of an Apple today is: \$HK $PRICE_PER_APPLE"
    
    # Avoiding ambiguity
    echo "The first 10 letters in the alphabet are: ${MyFirstLetters}DEFGHIJ"
    
    # Preserving whitespace
    echo $greeting
    echo "$greeting"
    ```
    
3.  Save the file.
    
4.  Run the script:
    
    ```bash
    ./variables.sh
    ```
    
    You should see the following output:
    
    ```
    The price of an Apple today is: $HK 5
    The first 10 letters in the alphabet are: ABCDEFGHIJ
    Hello world!
    Hello        world!
    ```
    
    Note the differences:
    
    -   The  `$`  sign is escaped in the first line to print it literally.
    -   Curly braces  `{}`  are used to clearly define the variable name in the second line.
    -   The last two lines show the difference between using quotes and not using quotes when referencing a variable with whitespace.

### Command Substitution

Command substitution allows you to use the output of a command as the value of a variable. This is done by enclosing the command with  `$()`  or backticks (``).

1.  Open the  `variables.sh`  file in the WebIDE.
    
2.  Add the following content to the end of the file:
    
    ```bash
    # Command substitution
    CURRENT_DATE=$(date +"%Y-%m-%d")
    echo "Today's date is: $CURRENT_DATE"
    
    FILES_IN_DIR=$(ls)
    echo "Files in the current directory:"
    echo "$FILES_IN_DIR"
    
    UPTIME=$(uptime -p)
    echo "System uptime: $UPTIME"
    ```
    
3.  Save the file.
    
4.  Run the script:
    
    ```bash
    ./variables.sh
    ```
    
    You should see output similar to this (the actual values will depend on your system):
    
    ```
    Today's date is: 2023-08-16
    Files in the current directory:
    variables.sh
    System uptime: up 2 hours, 15 minutes
    ```
    
    In this example:
    
    -   `$(date +"%Y-%m-%d")`  runs the  `date`  command and captures its output.
    -   `$(ls)`  runs the  `ls`  command and captures its output.
    -   `$(uptime -p)`  runs the  `uptime`  command with the  `-p`  option and captures its output.

### Arithmetic Operations

Shell variables can also be used in arithmetic operations. Bash provides the  `$((expression))`  syntax for performing arithmetic.

1.  Create a new file named  `arithmetic.sh`  in the  `/home/labex/project`  directory:
    
    ```bash
    touch /home/labex/project/arithmetic.sh
    ```
    
2.  Open the  `arithmetic.sh`  file in the WebIDE and add the following content:
    
    ```bash
    #!/bin/bash
    
    X=10
    Y=5
    
    # Addition
    SUM=$((X + Y))
    echo "Sum of $X and $Y is: $SUM"
    
    # Subtraction
    DIFF=$((X - Y))
    echo "Difference between $X and $Y is: $DIFF"
    
    # Multiplication
    PRODUCT=$((X * Y))
    echo "Product of $X and $Y is: $PRODUCT"
    
    # Division
    QUOTIENT=$((X / Y))
    echo "Quotient of $X divided by $Y is: $QUOTIENT"
    
    # Modulus (remainder)
    REMAINDER=$((X % Y))
    echo "Remainder of $X divided by $Y is: $REMAINDER"
    
    # Increment
    X=$((X + 1))
    echo "After incrementing, X is now: $X"
    
    # Decrement
    Y=$((Y - 1))
    echo "After decrementing, Y is now: $Y"
    ```
    
3.  Save the file.
    
4.  Make the script executable:
    
    ```bash
    chmod +x /home/labex/project/arithmetic.sh
    ```
    
5.  Run the script:
    
    ```bash
    ./arithmetic.sh
    ```
    
    You should see the following output:
    
    ```
    Sum of 10 and 5 is: 15
    Difference between 10 and 5 is: 5
    Product of 10 and 5 is: 50
    Quotient of 10 divided by 5 is: 2
    Remainder of 10 divided by 5 is: 0
    After incrementing, X is now: 11
    After decrementing, Y is now: 4
    ```
    
    This script demonstrates various arithmetic operations using shell variables.

### Using Environment Variables

Environment variables are a type of variable that is available to all processes running in the current shell session. Let's explore some common environment variables and how to create our own.

1.  Create a new file named  `environment.sh`  in the  `/home/labex/project`  directory:
    
    ```bash
    touch /home/labex/project/environment.sh
    ```
    
2.  Open the  `environment.sh`  file in the WebIDE and add the following content:
    
    ```bash
    #!/bin/bash
    
    # Displaying some common environment variables
    echo "Home directory: $HOME"
    echo "Current user: $LOGNAME"
    echo "Shell being used: $SHELL"
    echo "Current PATH: $PATH"
    
    # Creating a new environment variable
    export MY_VARIABLE="Hello from my variable"
    
    # Displaying the new variable
    echo "My new variable: $MY_VARIABLE"
    
    # Creating a child process to demonstrate variable scope
    bash -c 'echo "MY_VARIABLE in child process: $MY_VARIABLE"'
    
    # Removing the environment variable
    unset MY_VARIABLE
    
    # Verifying the variable is unset
    echo "MY_VARIABLE after unsetting: $MY_VARIABLE"
    ```
    
3.  Save the file.
    
4.  Make the script executable:
    
    ```bash
    chmod +x /home/labex/project/environment.sh
    ```
    
5.  Run the script:
    
    ```bash
    ./environment.sh
    ```
    
    You should see output similar to this (the actual values will depend on your system):
    
    ```
    Home directory: /home/labex
    Current user: labex
    Shell being used: /bin/zsh
    Current PATH: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
    My new variable: Hello from my variable
    MY_VARIABLE in child process: Hello from my variable
    MY_VARIABLE after unsetting:
    ```
    
    This script demonstrates how to access existing environment variables, create new ones, and remove them.