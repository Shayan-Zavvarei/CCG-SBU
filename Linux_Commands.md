 Basic:
 sudo apt-get install software -> Installing software on Linux
 -----------------------------------------------------------------------------------
 
 Directories:
 / -> root
 . -> Current Directory
 .. -> Parent Directory 
 /bin -> binary or other executalbe programs
 /etc -> system configuration files
 /home -> home directories
 /opt -> optional or third party softwares
 /tmp -> temporary space, typically cleared on reboot
 /usr -> user related programs
 /var -> variable data, most notably log files
 /boot -> files needed to boot the operating system
 /cdrom -> mount point for CD-ROMs
 /dev -> device files, typically controlled by the operating system and the system administrators.
 /lib -> system libraries
 /lib64 -> system libraries, 64 bit
 /lost+found -> Used by the file system to store recovered files after a file system check has been performed.
 /media -> Used to mount removable media like CD-ROMs
 /mnt -> used to mount external file systems.
-----------------------------------------------------------------------------------
 Application Directory Structures:
 /usr/local/application/bin
 /usr/local/application/etc
 /usr/local/application/lib
 /usr/local/application/log
 /opt/application/bin
 /opt/application/etc
 /opt/application/lib
 /opt/application/log
------------------------------------------------------------------------------------
 Permiassions:
 -To read a file, you need to have read (r) permission for that file.
 -To write to a file, to modify a file, or to erase a file, you need to have write (w) permission for that file.
 -To run a program or to change to a directory, you need to have execute (x) permission for that program or directory.
 chmod -> change mode(permission) command
 ugoa -> user category(user, groupe, other, all)
 rwx -> read write execute
 -----------------------------------------------------------------------------------
 
 Terminal Commands :
 Command Structure -> command_name -options inputs
 ctrl + L -> clear Screen
 history -> shows the command history in terminal
 pwd -> shows you current directory 
 exit -> exit the shell or your current session 
 clear -> clear the screen
 reset -> resets the shell or you current session 
 which -> locate a command 
 man -> shows the manual of a command *NOTE: in manual pages:Anything inside <> is mandatory. Anything inside [] is optional
 mkdir -> make directory
 touch -> creat new empty files
 rm -> remove or deletete file 
 rm -r -> delete directories 
 rm -ri -> To ask shell to prompt before every removal
 rmdir -> remove directory 
 cd -> change directory 
 ls -> shows files and directories 
 ls -a -> listing all files including hidden files
 ls -F -> listing files by type
 ls -t -> list files by time 
 ls -r -> shows in reverse order 
 ls - ltrh -> shows files and directories in humand way and sorted by time
 tree -> similar to ls but creats visual outputs 
 clear -> clear the screen 
 cat -> shows content of a file 
 cp file1 file2 -> copy file1 to file2 
 mv file1.txt file_renamed.txt -> rename a file name 
 mv file1.txt -> move a file from one directory to another 
 mv -r dir dir_renamed -> for moving directories 
 mv -r dir/* /home/user/Desktop
 rm -> remove files 
 sudo command -> runs command with root access 
 su - -> become root in ubuntu 
 chmod -> change mode(permission) command
 chmod +x task.sh --> make script executable
 ugoa -> user category(user, groupe, other, all)
 rwx -> read write execute
 less -> show a huge file (or a small file ) page by page with control
 grep -> fine one string in a file and show cntaining lines 
 echo -> print text in terminal
 cal -> calender in terminal 
 date -> shows the date in terminal
 tee -> Saving output while piping
 alias -> Defining a nickname for a command 
 file -> tell us basically what type of file we are dealing with
 nano filename.txt -> open a file (or create a new one)
 grep 'word' filename -> search and find 'word' in a file
 grep -i 'word' file -> case-insensetive search
 grep -r 'word' -> recursive search in current directory 
 grep -R 'word' -> same as -r, also follos symbolic links
 grep -n 'word' file -> Shows line numbers
 grep -w 'word' file -> Match only whole words
 grep -v 'word' file -> Invert match (show lines without "word")
 grep --color 'word' file -> Highlight matched text
 tar -c -> creat a new archive 
 tar -f -> specify filename 
 tar -x -> Extract archive 
 tar -t -> list contents
 tar -v -> verbose outputs  
 gzip archive.tar -> compress (deletes original by default)
 gzip -k archive.tar -> keep original
 gzip -d archive.tar.gz -> decompress
 gzip -r * -> compress all files recursively
 gzip -l archive.tar.gz -> show info
 gzip -9 archive.tar -> max compression
 echo $SHELL --> shows the default shell of your system
 $(command) --> assign command output to a variable
 ----------------------------------------------------------------------------------------
 Piping:
What if you wanted to connect the standard output of one command so that it flowed into  the standard input of another command? Well that's where piping comes
command1 | command2 | command3 .example:
 date | cut --delimiter " " --fields 1 > today.txt
 tee -> Saving output while piping:
 date | tee fulldate.txt | cut --delimiter " " --fields 1
 xargs -> for Commands That Don’t Accept STDIN (echo or rm):
 date | xargs echo
 ----------------------------------------------------------------------------------------
 Nicknaming using alias:
Aliases are custom shortcuts for commands or command pipelines. They help you save time by reducing repetitive typing and increasing productivity. example:
1. alias shortName="your custom command here"
2. alias ll="ls -l"
alias home="ssh -i ~/.ssh/mykey.pem user@192.168.0.100"
-----------------------------------------------------------------------------------------
 Wildcards in Linux:
Wildcards are special characters used in the terminal to match patterns when specifying filenames or paths.
 * -> Matches zero or more characters
 ? -> Matches exactly one character
 [] -> Matches any one character inside brackets
 {} -> Matches any pattern or name separated by commas
 [^] -> Matches any character NOT listed
 \ -> Escapes a special character
-----------------------------------------------------------------------------------------
 Brace Expansion:
Brace expansion is a mechanism by which arbitrary strings may be generated. example:
 mkdir {jan,feb,mar,apr,may,jun,july,aug,sep,oct,nov,dec}_{2017,2018,2019,2020,2021,2022}

 Nano Editor:
Nano is a simple and user-friendly command-line text editor for Linux
 nano filename.txt -> open a file (or create a new one)
Nano’s global configuration file is located at:
 /etc/nanorc
-----------------------------------------------------------------------------------------
 Search for Files:
To search for anything whether it be a file, al line or multiple lines in files is grep
(globally search for a regular expression and print matching lines). 
 grep [options] "pattern" filename, example:
 grep 'word' filename -> search and find 'word' in a file
 grep -i 'word' file -> case-insensetive search
 grep -r 'word' -> recursive search in current directory 
 grep -R 'word' -> same as -r, also follos symbolic links
 grep -n 'word' file -> Shows line numbers
 grep -w 'word' file -> Match only whole words
 grep -v 'word' file -> Invert match (show lines without "word")
 grep --color 'word' file -> Highlight matched text
 fgrep 'word-to-search' file.txt
 grep 'word' file1 file2 file3
 grep 'string1 string2'  filename
 cat otherfile | grep 'something'
 command | grep 'something'
 command option1 | grep 'data'
 grep --color 'data' fileName
 grep [-options] pattern filename
 fgrep [-options] words file
-----------------------------------------------------------------------------------------
 Archiving and Compression:
Archiving means combining multiple files/directories into a single file (called an archive) --> for backups  or transfering data between systems
 tar: tar is a Unix command representative TAPE Archive (tape archive). 
 tar -c -> creat a new archive 
 tar -f -> specify filename 
 tar -x -> Extract archive 
 tar -t -> list contents
 tar -v -> verbose outputs  
Examples:
 tar x -f archive.tar -C output_dir
 tar xf archive.tar -C output_dir
 tar xfv archive.tar -C output_dir      
**********************************
 gzip: Reduces file size.   
 gzip archive.tar -> compress (deletes original by default)
 gzip -k archive.tar -> keep original
 gzip -d archive.tar.gz -> decompress
 gzip -r * -> compress all files recursively
 gzip -l archive.tar.gz -> show info
 gzip -9 archive.tar -> max compression
 
-----------------------------------------------------------------------------------------
 Bash Scripting:
 * A shell is a command-line interpreter that allows users to interact with the operating system.
 * Scripting automates repetitive tasks by grouping multiple commands into a single file (a script).
 * Default shell in linux is:
 - echo $SHELL
 * Creating new bash script:
 1. nano task.sh --> creat new file 
 2. #!/bin/bash
    date
    cal
    pwd
    ls
 3. chmod +x task.sh --> make script executable
 4. ./task.sh --> Run the task
 
 * Variables are used to store and reuse data in a script or command line:
 - user=$(whoami)
 - day=$(date +%A)
 * You can print them using echo : echo "$greeting back $user! Today is $day."
 * Prefix variables with $ to access their value.
 * You can even assign command output to a variable: $(command)
 
 * Conditional Statements:
 -if [ $num_a -lt $num_b ]; then
      echo "$num_a is less than $num_b!"
  else
      echo "$num_a is greater than $num_b!"
  fi
 
 * For loops:
 - #!/bin/bash
   for i in 1 2 3; do
       echo $i
   done

 * While loops:
 -#!/bin/bash
  counter=0
  while [ $counter -lt 3 ]; do
      let counter+=1
      echo $counter
  done
-----------------------------------------------------------------------------------------
 Bash Functions
 * Functions let you group multiple commands into a single reusable block.
 * We have two format to define a function:
 1. function_name() {
    # commands go here} 
 2. function function_name {
    # commands go here}
 * for finding the output of functions, you can use echo (same as print in python):
 - get_value() {
  result="42"}
  get_value
  echo $result  # Output: 42
 