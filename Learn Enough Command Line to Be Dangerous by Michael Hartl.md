# Learn Enough Command Line to Be Dangerous by Michael Hartl

## Basics
- *ssh* - secure shell
- Terminal and iTerm are examples of macOS command line programs
- Anatomy of a command line
  - prompt (no need to type and provided automatically by the terminal)
  - command
  - option
  - argument
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

- `>` - redirect (`echo "hello world" > hello.txt` redirects the string into a new .txt file)
