# sshsudo: ssh+sudo

* This is a clone of Jiwu Liu's sshsudo project from http://code.google.com/p/sshsudo/.


Introduction
----
Usually it takes a system administrator three steps to run a command with root privilege on a remote computer:

 1. ssh to a remote computer
 2. sudo a command
 3. exit

Alternatively, if a user script is to be executed with root privilege on the remote computer, it has to be copied there first. Therefore four steps are needed.

 1. scp the script to the remote computer
 2. ssh to the remote computer
 3. sudo the script
 4. exit

The authentication is required for almost every step. Thus a system administrator needs to type in his/her password two or three times for a single task.

There are some ways to save keystrokes when doing the remote administration. For example, sudo can be directly executed through ssh without login: ssh foo@bar.net sudo ls -l However, the system administrator stills needs to type in his/her password twice, once for ssh and then for sudo. To make it even worse, at the second time the password (to sudo) is echoed in the terminal. Moreover, it can not spare the manual copy if it is the user script that is to be executed remotely.

The ultimate solution seems to be to enable the passwordless operation for both ssh and sudo. In addition, scripts can be stored on a NFS partition shared by all computers in the local network, so that the manual copy of the scripts are not required. However, this procedure may be somewhat an overkill. Also it may undermine the system security.

Solution
---
The script **sshsudo** is designed to solve this problem. It can perform the administration task on one or more remote computers conveniently. It utilizes a software sshpass which can be installed by apt-get on Ubuntu. The argumens to this script are basically the sudoer names, the computer names and the command. The password, for security reason, is read in from the standard input only. Due to the fact that a system administrator of multiple computers often uses the same sudoer name and password, **sshsudo** asks for the password only once even when more than one remote computer is specified in the command line. The same password is used for all computers. Thus an extra saving of keystrokes can be achieved.

**sshsudo** leads to a significant saving of keystrokes. For example, if a system administrator is about to execute one user script on ten remote computers, s/he needs to type in his/her password for 3x10=30 times by using ssh,scp and sudo. With the script **sshsudo**, the password is required only once, e.g.:

```bash
sshsudo -r -u foo comp1,comp2,comp3,comp4,comp5,comp6,comp7,comp8,comp9,comp10 /path/myscript Please enter your password:
```

After the prompt, the system administrator types in his/her password once and for all. The script "/path/myscript" will be copied and executed on 10 remote computers automatically.

Features and Usage
---
**sshsudo** has many nice features:

 * **Interactive execution**: **sshsudo** connects the standard input and output of the remote script to the local terminal for the interactive execution.
 * **Secure password handling**: **sshsudo** does not store or show user password by any means. The password is solely used inside sshsudo and discarded after sshsudo ends.
 * **Easy to use**: **sshsudo** does not require any installation, any configuration (such as passwordless ssh by public/private keys). You can simply download the single script and start to use it immediately.

The detailed usage of **sshsudo** is:
```
sshsudo -r -v [-u user] AccountList Command [Arguments]
```
 * -u User: Set the default user unless it is given within remote account list
 * -r: Copy the user script to remote computers to execute
 * -v: Verbose output
 * AccountList: [user1@]computer1,[user2@]computer2,[user3@]computer3,... or the name to a file which contains accounts(user@computer) in separate lines, such as: [user1@]computer1 [user2@]computer2 [user3@]computer3 [user4@]computer4 [user5@]computer5 ... ...
 * Command: The command/script to be executed
 * Arguments: All arguments to be passed to the command/script (separated by spaces)

Example 
---
A simple example is shown below(with screen output), where sshsudo is used to execute "apt-get autoremove": 
```
sshsudo -u foo comp1,comp2,comp3,comp4,comp5,comp6,comp7 apt-get autoremove Please enter your password: ===== foo@comp1: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 17 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp2: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 131 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp3: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 65 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp4: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 418 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp5: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 48 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp6: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 49 not upgraded. ----------------------------------DONE!!---------------------------------------- ===== foo@comp7: sudo apt-get 'autoremove' [sudo] password for foo: Reading package lists... Building dependency tree... Reading state information... 0 upgraded, 0 newly installed, 0 to remove and 160 not upgraded. ----------------------------------DONE!!----------------------------------------
```
