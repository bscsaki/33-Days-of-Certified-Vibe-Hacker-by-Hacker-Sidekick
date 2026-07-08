### **Day 1: Log Analysis 3**

**Challenge:** Analyze 3.log to determine what two letter command is being used in this attack.

This is an audit log file, so let's analyze its contents.

Open the 3.log file and look through the lines. You will notice commands that look like linux bash.
![line1](images/line1.png)

| type=SYSCALL msg=audit(1604996384.965:93777): arch=c000003e syscall=59 success=yes exit=0 a0=558e25052c70 a1=558e251641a0 a2=558e25160800 a3=1b6 items=2 ppid=29002 pid=2168 auid=1000 uid=1000 gid=1000 euid=1000 suid=1000 fsuid=1000 egid=1000 sgid=1000 fsgid=1000 tty=pts0 ses=104 comm="dd" exe="/bin/dd" key=(null) |  |
| :---- | :---- |
| type \= SYSCALL | Since this is an audit log file the type lets you know that this is a **system call** type of record. A **system call**  is a direct request made to the kernel to do something.  The best reddit explanation said ‘your Mom doesn’t trust you with scissors, so you have to ask her to do anything that involves scissors’ And so the kernel does not trust you to perform system calls. |
| msg=audit(1604996384.965:93777) | This log comes from the linux audit framework, **auditd** and each log record gets a single **audit ID** like the number in the parentheses. Each other type of record that we will see below shares the same **audit ID** since they come from the same event |
| syscall=59 | Every system call has a fixed number, on linux **syscall 59** is execve. The **Execution Vector** (execve) wipes all the memory of the current process but keeps the same **pid**. Everything gets overwritten with a new binary.  |
| items=2 | This tells you how many PATH records show up in the log for this specified event. (more below) |
| ppid=29002 | This is the **P**arent **P**rocess **ID** of the process that actually made this **system call** and the **ppid** tells you which **parent process** started it in the first place |
| pid=2168 | This is the **P**rocess **ID** that actually made this system call. So the **ppid** called this **pid** and then the **syscall** was made. |
| auid=1000 | This is the **audit user ID** or  **login uid**, so each time a user logs in this ID stays the same (regardless of sudo) and is used to trace the origin chain of an activity. |
| uid=1000 | This is the **user ID** the process was actually running as at the moment of this **syscall**. It can be different from **auid** if the user switched identities. |
| … |  |
| tty=pts0 | **Tty**  or  **teletypewriter** is the general term for any terminal device on Unix. In the past it was used to be hardware that sent typed text over a wire **pts0** or  **pseudo terminal slave** is the modern virtual version of the teletypewriter. It is automatically created by the kernel when you  log in remotely ie SSH or open a terminal emulator window. The number **0** tracks the order of creation in each session |
| comm=”dd” | The **command** name as how kernel saved it |
| exe=”/bin/dd” | This is the full path to the binary file that got **executed** |

![line2](images/line2.png)

| type=EXECVE msg=audit(1604996384.965:93777): argc=4 a0="dd" a1="if=/dev/zero" a2="bs=1" a3="count=1" |  |
| :---- | :---- |
| type=EXECVE | Now **execve** different record type than the **syscall 59** in the line above, they describe the same event but from different perspectives. This **execve** record type captures the full argument list of the command that got executed. |
| argc=4 | Tells you to expect 4 arguments |
| a0=”dd” | This is the first argument and the command name itself |
| a1=”if=/dev/zero” | **if** means input file (**of** means output file not shown here)  **dev/zero** is 1\. a character and 2\. a pseudo device   A **character device** transfers data one **character** at a time. A **Character special file** acts as the interface to character devices. So the driver communicates with a **character device** by sending **single characters** as data to the **character special file**. (VS. There are also block special files that act as the interface to block devices, the driver sends block of data)  It’s a pseudo device, meaning that there is not any real hardware. It behaves like a device the system can read/write. With **dev/zero** being 1 and 2, a device with no hardware that transfers a single character of data per access, this file returns an infinite stream of null characters. One at a time. The **dev** directory consists of device files, which are abstractions of standard devices that applications interact with via I/O system calls (sending blocks or characters of data…) |
| a2=”bs=1” | Sets the block size to 1 byte, so when it runs it can read/write one byte at a time |
| a3=”count=1” | Tells dd to perform exactly one block operation. So per execution bs\*count \=1 aka only 1 byte per execution |


![line3](images/line3.png)

| type=CWD msg=audit(1604996384.965:93777): cwd="/home/wardog" |  |
| :---- | :---- |
| type=CWD | **C**urrent **W**orking **D**irectory. When you ask code to read/write it automatically looks for the cwd  |
| cwd=”/home/wardog” | This is the user’s location in the system when they run the command, but the file was still executed in its own location /bin/add |


![line4](images/line4.png)

| type=PATH msg=audit(1604996384.965:93777): item=0 name="/bin/dd" inode=20 dev=08:01 mode=0100755 ouid=0 ogid=0 rdev=00:00 nametype=NORMAL cap\_fp=0 cap\_fi=0 cap\_fe=0 cap\_fver=0 cap\_frootid=0 |  |
| :---- | :---- |
| type=PATH | This record type shows the files that the kernel accessed write/read to execute and complete the syscall of this log. |
| item=0 | This is the first of the two PATH records. From the first line we know there are 2 to come so this is the first one **0**. |
| name=”/bin/dd” | This is the actual file path the kernel accessed and where dd binary is stored and executed. |
| inode=20 | Every file on linux has an **in**dex **node** a unique ID for file metadata. The metadata stored is file size, owner, permissions, timestamps, pointers to disk blocks but no file name. The filesystem ID combines with the inode and creates a unique identification label. |
| dev=08:01 | This shows the storage device location of the file, **major:minor** is the format. So this is the location virtually or physically in the disk of **inode 20** |
| mode=0100755 | This is the file **type** and **permission bits**. 0100-755. The first part 0100 means that it is a regular file (if it was 0400 it would be a directory, or 0200 a character device like /dev/zero) and 755 shows the permissions for the owner, the user group, and everyone else in this order. Each number is a sum of permissions so 4 means read, 2 means write, 1 means execute. The owner is 7 so read, write, execute. The group and everyone else are 5, so read and execute, but not write.  |


![line5](images/line5.png)

| type=PATH msg=audit(1604996384.965:93777): item=1 name="/lib64/ld-linux-x86-64.so.2" inode=29514 dev=08:01 mode=0100755 ouid=0 ogid=0 rdev=00:00 nametype=NORMAL cap\_fp=0 cap\_fi=0 cap\_fe=0 cap\_fver=0 cap\_frootid=0 |  |
| :---- | :---- |
| type=PATH | The other file accessed by the kernel to complete syscall |
| item=1 | The second PATH  |
| name="/lib64/ld-linux-x86-64.so.2" | This is the **dynamic linke**r that needs to be loaded before the program can be executed. The binary dd was written to call functions that live inside shared libraries. dd uses library functions instead of having that code built directly into it, and so it cannot run on its own, it needs those libraries linked in at the moment it executes. The loader will locate and load all the library programs, resolve the function addresses between them, and then the binary can execute using them.  Resolving function addresses happens because the shared library functions' location varies depending on what is loaded on memory at the current moment. So the loader literally has to go look for each function, load it, and write the current memory address. |


![line6](images/line6.png)

| type=PROCTITLE msg=audit(1604996384.965:93777): proctitle=64640069663D2F6465762F7A65726F0062733D3100636F756E743D31  |  |
| :---- | :---- |
| type=PROCTITLE | This means **Proc**ess **Title** and is the record type that gets the entire command line  that triggered this event into a string. This is used as a redundancy measure. |
| 64640069663D2F6465762F7A65726F0062733D3100636F756E743D31 | These hex pairs are the 4 arguments encoded into one hexadecimal sequence. This is how linux stores the argv (mentioned before) in memory |
| ![decoded](images/decoded.png) | Using a hex decoder on the sequence it outputs the same result as the same arguments from the EXECVE record and it’s a way to confirm that our reconstruction is accurate. |

**Summary:**

In this challenge of [Certified Vibe Hacker Workshop](https://certifiedvibehacker.com/) by [Hacker SideKick](https://hackersidekick.com/) we had to work through a linux audit log and understand what this single event was doing. 

By looking though all of the different event record types we learnt more on how the linux OS operates, and saw a building block that can be used to scale an attack.

Commands built with **dev/zero** can be used to generate huge amounts of data and if there is an **of** file specified (there was not one here) the target file  can be overwritten or wiped  entirely.

Bibliography:  
[https://www.reddit.com/r/C\_Programming/comments/17rokbv/how\_would\_you\_explain\_to\_a\_5\_year\_old\_what\_a/](https://www.reddit.com/r/C_Programming/comments/17rokbv/how_would_you_explain_to_a_5_year_old_what_a/)  
[https://medium.com/@m1\_duronto/topic-deep-dive-into-system-call-59-mastering-execve-for-advanced-process-control-0b28a4d48630](https://medium.com/@m1_duronto/topic-deep-dive-into-system-call-59-mastering-execve-for-advanced-process-control-0b28a4d48630)  
[https://docs.redhat.com/en/documentation/red\_hat\_enterprise\_linux/7/html/security\_guide/sec-understanding\_audit\_log\_files](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/security_guide/sec-understanding_audit_log_files)  
[https://medium.com/@M4verick/how-i-went-from-confused-to-confident-understanding-ttys-ptys-ssh-and-tmux-d34252c0e452](https://medium.com/@M4verick/how-i-went-from-confused-to-confident-understanding-ttys-ptys-ssh-and-tmux-d34252c0e452)  
[https://unix.stackexchange.com/questions/21280/difference-between-pts-and-tty](https://unix.stackexchange.com/questions/21280/difference-between-pts-and-tty)  
[https://man7.org/linux/man-pages/man2/execve.2.html](https://man7.org/linux/man-pages/man2/execve.2.html)  
[https://linuxhandbook.com/dev-zero/](https://linuxhandbook.com/dev-zero/)  
[https://www.baeldung.com/linux/file-dev-zero-meaning](https://www.baeldung.com/linux/file-dev-zero-meaning)  
[https://en.wikipedia.org/wiki/Working\_directory](https://en.wikipedia.org/wiki/Working_directory)  
[https://www.redhat.com/en/blog/inodes-linux-filesystem](https://www.redhat.com/en/blog/inodes-linux-filesystem)  
[https://towardsdev.com/inode-in-linux-detailed-information-f829a078c3d7?gi=adf66f02b83c](https://towardsdev.com/inode-in-linux-detailed-information-f829a078c3d7?gi=adf66f02b83c)  
[https://en.wikipedia.org/wiki/Dynamic\_linker](https://en.wikipedia.org/wiki/Dynamic_linker)  
 [https://www.gabriel.urdhr.fr/2015/01/22/elf-linking/](https://www.gabriel.urdhr.fr/2015/01/22/elf-linking/)  
[Hex decoder: Online hexadecimal to text converter \- cryptii](https://cryptii.com/pipes/hex-decoder/) 

