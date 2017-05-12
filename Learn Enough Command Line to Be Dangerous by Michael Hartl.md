# Learn Enough Command Line to Be Dangerous by Michael Hartl

## Summary of my understanding
1. You launch the terminal program (Terminal, iTerm, etc) which hosts and automatically launches a shell program (bash, fish, etc) that provides an API (accessible via command line commands) for executing OS-level functionality.
2. This shell program launch is referred to as a shell session, one where temporary, in addition to persistent, Environment Variables are accessible (CRUD).
3. The PATH is a persistent Environment Variable denoting a list of OS directory paths where executable programs reside.
4. Commands can then be entered via the command line interface (CLI - the terminal with its shell) which are essentially function calls where:
  - The command denotes the function name
  - The option(s) denote an overridable config argument
  - The argument(s) denote the non-config core data to be operated against
  - The function execution results in a returned value that subsequent commands may leverage

## Basics
- *ssh* - secure shell
- Terminal and iTerm are examples of macOS command line programs
- Anatomy of a command line
  ![Anatomy of a command line](https://softcover.s3.amazonaws.com/636/learn_enough_command_line/images/figures/anatomy.png "Anatomy of a command line")
  - prompt (no need to type and provided automatically by the terminal), command, option, and argument
- Terminal vs. Console vs. Shell
  - terminal = text input/output environment
  - console = physical terminal
  - shell = command line interpreter
- `Ctrl-C` or `^C`- "cancel" to escape to a fresh prompt for entering a command
- *shell* - program used to run a command line
  - *man* - format and display the on-line manual pages, `man echo` = echo as argument resulting in listing of info about `echo`
  - *echo* - displays a string of text
- Keyboard mapping
  - Command	`⌘`
  - Control	`⌃`
  - Shift	`⇧`
  - Option	`⌥`
  - Up, down, left, right	`↑` `↓` `←` `→`
  - Enter/Return	`↵`
  - Tab	`⇥`
  - Delete	`⌫`
- Navigation shortcuts
  - `⌥-click` - direct cursor placement
  - `^A` - line start jump
  - `^E` - line end jump
  - `^U` - line delete
  - `clear` or `^L` - clear screen
  - `exit` or `^D` - exit terminal
  
## Manipulating files

- `>` - redirect (`echo "hello" > hello.txt` redirects the string into a new .txt file)
- `>>` - append (`echo "world" >> hello.txt` appends string to file)
- `cat` - concatenate (`cat hello.txt` allows quick-and-dirty output of a file)
- `diff` - compare file contents (`diff hello.txt world.txt`)
- `ls` - list files and directories in current directory 
  - `ls *.txt` list all txt files via wildcard
  - `ls -a` list all files (including hidden)
- `touch` - create empty file
- `mv` - move and rename (just renames if no move target directory)
- `cp` - copy
- `rm` - remove/delete
- `⇥⇥` - tab completion (super useful autofill/suggest)

## Inspecting files

- `curl` - fetch URL
- `ping` - pings URL continually
- `head` - view first 10 lines of file
- `tail` - view last 10 lines of file
- `|` - pipes command result as arg to next command (`head sonnets.txt | wc`)
- `grep` - file pattern searcher (`-r` powerful recursion option useful in traversing directories)
- `~` - home directory shorthand
- "`sudo` gives ordinary users the power to execute commands as the superuser"
- `mkdir` - make directory
- `cd` - change directory
  - `.` - current directory (`cp ~/text_files/sonnets.txt .` copies sonnets.txt to current directory or `open .` opens finder to current directory)
  - `cd ..` - move up one directory
  - `cd` or `cd ~` - go to root user directory
  - `cd -` - jump to previously focused directory
- `pwd` - print working directory


## Understanding Environment Variables and the Unix Path
*This section is from https://cbednarski.com/articles/understanding-environment-variables-and-the-unix-path/*
- "When you type the name of a program `cat` or `grep`, your shell looks in all the directories specified in your `PATH` to try to find a matching program. `PATH` itself is an environment variable (other common ones include `EDITOR` and `JAVA_HOME`). Environment variables are global variables that live in your shell session, and help your shell fill in shortcuts or specify preferences."
- "You can dynamically set (or change) environment variables using export, or persistently set the values in your `~/.bash_profile` or `~/.bashrc` file. For example, we set `EDITOR` to vim, then use `$EDITOR` to invoke vim on our `.bash_profile` so we can persist some other environment variables:
  ```
  $ export EDITOR=vim
  $ $EDITOR ~/.bash_profile
  export PATH=$PATH:/something/i/need/to/add
  export NEW_ENVIRONMENT_VARIABLE=value
  ```
  Environment variables are read from `.bash_profile` at the start of the shell session, so after you save the file you'll need open a new terminal window before the changes will take effect."
- "`~/.bash_profile` is actually a bash script which, by convention, is executed every time bash starts up (i.e. whenever you open a new terminal)"
