# Command Line Interpreter

###### What is it
* a blanket term for a certain class of programs designed to accept commands from a user

###### What is it used for
* ***it is used to operate software and operating systems***
* gives user a more direct way to interact with the OS
* more commands are available than with a Graphical User Interface
* allows user to automate commands/processes using scripts


# BASH
* **B**ourne **A**gain **Sh**ell

# Shell
* an interpreter that accepts commands from a user

# .bashrc File

###### What is it

* a shell script that is executed every time a new shell is opened up
* every time you open a terminal, the bash .bashrc file is executed


###### What is it used for
* used to initialize a bash session and prepare shell




###### How does it work
* commands are placed inside to initialize a session with whatever information is needed

Example:
* `export PATH=$PATH:/home/petrarca95/Documents/programs/jdk1.8.0_221/bin`

Here we set the PATH variable to the path containing the JDK in order to execute java commands from the terminal



#### Common things to do in a .bashrc file
1. set up Aliases to commonly used commands

```BASH
# added by Miguel August 25 2019 to give alias in order to open files easier
alias open='xdg-open'
```
