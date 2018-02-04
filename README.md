# UnderstandingAuditdRules
#### This is an overview of writing a sample auditd rule for Linux. 

This example shows how to monitoring the duration a file exists within the /tmp directory.

Create a single rule file /etc/audit/rules.d/tmp.rules with the sole entry 

```
-w /tmp -p wa -k files_in_tmp
```

Which can be understood as follows: 
- “-w /tmp” means “watch /tmp”
- “-p wa” means “when the permissions are either write or change attributes to a file”
- “-k files_in_tmp” means “add a string to the log entry with the keyname 'files_in_tmp' so we can search the log for these entries”. 

Once this rule is in place it requires a restart of the audit daemon. 

Note - One may need to do the following to get the audit.d, upon restart, to read the rules in from /etc/audit/rules.d/

Red Hat Enterprise Linux 6

Set in /etc/sysconfig/auditd
```
USE_AUGENRULES=no
```
Restart the auditd service: 
```
service auditd restart
```

Red Hat Enterprise Linux 7
```
cp /usr/lib/systemd/system/auditd.service /etc/systemd/system/auditd.service
```
then edit /etc/systemd/system/auditd.service according to the comments in the file
Raw
```
 ## To not use augenrules, copy this file to /etc/systemd/system/auditd.service
 ## and comment/delete the next line and uncomment the auditctl line.
 ## NOTE: augenrules expect any rules to be added to /etc/audit/rules.d/
###  ExecStartPost=-/sbin/augenrules --load <--- THIS GETS COMMENTED OUT
ExecStartPost=-/sbin/auditctl -R /etc/audit/audit.rules # <--- THIS GETS UNCOMMENTED
```

Reload systemd daemon to reload changes made in auditd service unit file:

```
$ systemctl daemon-reload
```
and/or
```
Restart the auditd service: service auditd restart
```
Upon restart of the daemon there is an entry in the log showing the rule was added. 
```
type=CONFIG_CHANGE msg=audit(1512079682.652:109082): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="files_in_tmp" list=4 res=1
```

Create a sample file called foo in /tmp
```
touch /tmp/foo 
```

This creates the following log file entry
```
type=SYSCALL msg=audit(1512079784.674:109091): arch=c000003e syscall=2 success=yes exit=3 a0=7fffca5e88c6 a1=941 a2=1b6 a3=7fffca5e6a60 items=2 ppid=18028 pid=21237 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

Editing the file foo with the text editor "vi"
```
[root@ip /]# vi /tmp/foo
```
Creates the following log entries. 
```
type=SYSCALL msg=audit(1512079791.931:109092): arch=c000003e syscall=2 success=yes exit=4 a0=1616720 a1=c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109093): arch=c000003e syscall=2 success=yes exit=5 a0=15f7520 a1=c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109094): arch=c000003e syscall=87 success=yes exit=0 a0=15f7520 a1=7ffffc461ce0 a2=7ffffc461ce0 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109095): arch=c000003e syscall=87 success=yes exit=0 a0=1616720 a1=7ffffc461ce0 a2=7ffffc461ce0 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109096): arch=c000003e syscall=2 success=yes exit=4 a0=1616720 a1=200c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079798.870:109097): arch=c000003e syscall=2 success=yes exit=3 a0=15f9710 a1=241 a2=1a4 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079798.872:109098): arch=c000003e syscall=87 success=yes exit=0 a0=15f7520 a1=1 a2=1 a3=1 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

Next, removing the test file "foo" created
```
[root@ip /]# rm /tmp/foo
rm: remove regular file ‘/tmp/foo’? y
```

Creates the following log entries. 
```
type=SYSCALL msg=audit(1512079810.810:109106): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=21520c0 a2=0 a3=7fff479577a0 items=2 ppid=18028 pid=21245 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

Making the directory "foodir"
```
[root@ip /]# mkdir /tmp/foodir
```

Creates the following
```
type=SYSCALL msg=audit(1512079821.330:109107): arch=c000003e syscall=83 success=yes exit=0 a0=7ffe9636c8c3 a1=1ff a2=1ff a3=7ffe9636a980 items=2 ppid=18028 pid=21246 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="mkdir" exe="/usr/bin/mkdir" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

Creating the file foofile in the new directory "foodir" to show that subdirectories will be picked up by the same rule
```
[root@ip /]# touch /tmp/foodir/foofile
```
Creates the following
```
type=SYSCALL msg=audit(1512079836.755:109108): arch=c000003e syscall=2 success=yes exit=3 a0=7fffca9448bb a1=941 a2=1b6 a3=7fffca943290 items=2 ppid=18028 pid=21247 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

And lastly removing the file in the subdirectory
```
[root@ip /]# rm /tmp/foodir/foofile
rm: remove regular empty file ‘/tmp/foodir/foofile’? y
```
Creates the following
```
type=SYSCALL msg=audit(1512079845.198:109109): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=11090c0 a2=0 a3=7ffc372db250 items=2 ppid=18028 pid=21248 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

#### Notice these logs were grepped for the key "files_in_tmp"
This will show the Syscall but not the PATH statements (This is like having the verb in a sentence, without the noun) 

#### Fortunately these logs are well structured and the missing event information (not key tagged) can be correlated by the timestamp and event ids. 
```
This part -> msg=audit(1512079845.198:109109)
or more specifically -> "1512079845.198:109109"
```

Let us look at this id to see the file that was removed (pretending we didn't know it was /tmp/foodir/foofile)
```
 grep 1512079845.198:109109 /var/log/audit/audit.log.1
type=SYSCALL msg=audit(1512079845.198:109109): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=11090c0 a2=0 a3=7ffc372db250 items=2 ppid=18028 pid=21248 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=CWD msg=audit(1512079845.198:109109):  cwd="/"
type=PATH msg=audit(1512079845.198:109109): item=0 name="/tmp/foodir/" inode=94714 dev=ca:02 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=PARENT
type=PATH msg=audit(1512079845.198:109109): item=1 name="/tmp/foodir/foofile" inode=94715 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PROCTITLE msg=audit(1512079845.198:109109): proctitle=726D002D69002F746D702F666F6F6469722F666F6F66696C65
```
#### This block, shows more interesting and human readable events and paints a complete picture of who did what to which file, when.
This can act as a jump off point for calculating the file lifetime as well as what specifically is creating the files. 

Alternatively, if there isn't robust audit logging already in place (other logs to contend with), and using the key tags is not required 
simply grepping for the 'CREATE\|DELETE' string, redirecting the output to a single file, should prove adequate for getting the file lifetime (sorting on name, time, and objtype per entry, as needed). 

#### It should be noted that when writing to a file using a text editor there are a lot of CREATES and DELETES that occur on the .swp file, as it gets consistently recreated. These would greatly skew any averaging.

```
grep 'CREATE\|DELETE' /var/log/audit/audit.log.1
type=PATH msg=audit(1512077978.266:108846): item=1 name="foo" inode=12818926 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512078001.433:108854): item=1 name="foo" inode=12818926 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512078028.481:108855): item=1 name="foodir" inode=54554503 dev=ca:02 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512078058.781:108857): item=1 name="/tmp/foodir/foofile" inode=54554504 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079639.420:109060): item=1 name="/tmp/foodir/foofile" inode=54554504 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079647.426:109062): item=1 name="/tmp/foodir" inode=54554503 dev=ca:02 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079682.635:109075): item=1 name="/tmp/aurules.ELI4cPCR" inode=12818926 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079682.651:109078): item=1 name="/tmp/aurules.ELI4cPCR" inode=12818926 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079784.674:109091): item=1 name="/tmp/foo" inode=12818926 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079791.931:109092): item=1 name="/tmp/.foo.swp" inode=12818930 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079791.931:109093): item=1 name="/tmp/.foo.swpx" inode=12818931 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079791.931:109094): item=1 name="/tmp/.foo.swpx" inode=12818931 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079791.931:109095): item=1 name="/tmp/.foo.swp" inode=12818930 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079791.931:109096): item=1 name="/tmp/.foo.swp" inode=12818930 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079798.872:109098): item=1 name="/tmp/.foo.swp" inode=12818930 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079810.810:109106): item=1 name="/tmp/foo" inode=12818926 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512079821.330:109107): item=1 name="/tmp/foodir" inode=94714 dev=ca:02 mode=040755 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079836.755:109108): item=1 name="/tmp/foodir/foofile" inode=94715 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512079845.198:109109): item=1 name="/tmp/foodir/foofile" inode=94715 dev=ca:02 mode=0100644 ouid=0 ogid=0 rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE
type=PATH msg=audit(1512083182.319:109525): item=1 name="/tmp/aurules.wG5WMtXt" inode=12818926 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:tmp_t:s0 objtype=CREATE
type=PATH msg=audit(1512083182.334:109528): item=1 name="/tmp/aurules.wG5WMtXt" inode=12818926 dev=ca:02 mode=0100600 ouid=0 ogid=0 rdev=00:00 obj=system_u:object_r:tmp_t:s0 objtype=DELETE
```

 Only if we are planning on further finding out what initiated the file creation is the deeper insight of value. 








