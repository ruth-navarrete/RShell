# CS 100 Programming Project

## Project Information for CS100 RShell
* Fall 2019
* Hiyab Woldegebriel, SID 862098830 
* Ruth Navarrete, SID: 862106344

## Introduction
Utilizing Composite Pattern, develop a command shell called rshell in C++ which is capable of performing the following steps:

1. Print a command prompt (e.g. `$`)
1. Read in a line of command(s) (and connector(s)) from standard input
1. Execute the appropriate commands using `fork`, `execvp`, and `waitpid`

Commands will have the following format (note that the square brackets represent optional portions of an input):

`$ executable [argumentList] [connector] [executable] ...`

Where there can be any number of commands (which are composed of executables and argument lists) separated by either ||, && or ; which are the only valid connectors.

When executing a line of commands, you will need to account for the following requirements:

1. Execute the commands using the syscalls fork, execvp, and waitpid. Previous cs100 students created two video tutorials a fun cartoon tutorial as well as a more serious explanation, and should refer to the man pages for detailed instructions.
2. Every time you run a syscall, you must check for an error condition and if an error occurs then call perror. For examples on when, how, and why to use perror, see this video tutorial or the official man page.
3. You must have a special built-in command of `exit` which exits your program. This command should also adhere to the connector rules when deciding when/if it should be executed.
4. Anything that appears after a # character in the line should be considered a comment. For example, in the command `ls -lR` /, you would execute the program `/bin/ls` (performed using execvp) passing into it the parameters `-lR` and `/`. But in the command `ls # -lR /`, you would execute `/bin/ls`, but you would not pass any parameters because they appear in the comment section. You should also note that the # may or may not be followed by a space before the comment begins

## Diagram
![Image of Diagram](https://github.com/cs100/assignment-yabbie_ruth/blob/master/images/Updated%20Design%20Assignment%20Diagram.png?raw=true)

## Classes
**Command**: Command is the component class. It has a `virtual int run(Command*) = 0` function that will be implemented by inhereited classes. It also has a string name_com that is mainly utilized by the Connector class.

**Executable**: Executable is a leaf class. It will implement `virtual int run(Command*)` by first forking and then calling `execvp(...)` on the process child. The `execvp(...)` will replace the current process with the process specified by the Command* parameter.

**Connector**: Connector is a composite class. First, it will output the commmand prompt (e.g `$`) and then wait for user input. From the user input, it will parse through, determining what input will create what objects. For example, if first of the user input is an Executable, it will then take in any arguments up until a connector and create an Executable. Once a connector is encountered in the user input, the Executable will be pushed onto a vector of Command* and then a Connector will be created and pushed onto the same vector. The populated vetor is in the pattern of executable, connector, executable... so on so forth. 

In terms of implementing the `run(Command*)` function, Connector calls run on an executable, and depending on the result given by `execvp` as well as the current connector, will iterate through the vector of Command* (executable, connector, executable...) and call either the functions conditional_both for the && connector, xor_connector for the || connetor, and and_connector for the ; connector. These mentioned functions will also implement `virtual int run(Command*)` by calling run on each individual executable and actually executing it. Conditional_both will use the return from the previous Executable to determine if the next Executable will be run (if the previous Executable succeeds the next Executable will run). Xor_connector will use the return of the previous Execuable to determine if the next Executable will run (if the previous Executable fails, the next Executable will run). And_connector will run the next Executable regardless of the success previous Executable.
