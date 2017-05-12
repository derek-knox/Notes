# Learn Enough Command Line to Be Dangerous by Michael Hartl

## Basics
- *ssh* - secure shell
- Terminal and iTerm are examples of macOS command line programs
- Anatomy of a command line
  ![Anatomy of a command line](https://softcover.s3.amazonaws.com/636/learn_enough_command_line/images/figures/anatomy.png "Anatomy of a command line")
  - prompt (no need to type and provided automatically by the terminal)
  - command
  - option
  - argument
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

- 
