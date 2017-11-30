# UnderstandingAuditdRules
This is an overview of writing auditd rules for Linux. 

This example shows how to monitoring the duration a file exists within the /tmp directory.

Create a single rule file /etc/audit/rules.d/tmp.rules with the sole entry 

```
-w /tmp –p –w –k files
```

Which can be understood as follows: 
- “-w /tmp” means “watch /tmp”
- “-p –w” means “when the permissions are writing to a file”
- “-k tmpfiles” means “add a string to the log entry with the keyname tmpfiles so I can search the log for these entries”. 

Once this rule is in place it requires a restart of the audit daemon. 

Upon restart of the daemon there is an entry in the log showing the rule was added. 
```
type=CONFIG_CHANGE msg=audit(1512079682.652:109082): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="files_in_tmp" list=4 res=1
```

#### Ceate a sample file called foo in /tmp
```
touch /tmp/foo 
```

#### This creates the following log file entry
```
type=SYSCALL msg=audit(1512079784.674:109091): arch=c000003e syscall=2 success=yes exit=3 a0=7fffca5e88c6 a1=941 a2=1b6 a3=7fffca5e6a60 items=2 ppid=18028 pid=21237 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

#### Editing the file foo with the text editor "vi"
```
[root@ip /]# vi /tmp/foo
```
#### Creates the following log entries. 
```
type=SYSCALL msg=audit(1512079791.931:109092): arch=c000003e syscall=2 success=yes exit=4 a0=1616720 a1=c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109093): arch=c000003e syscall=2 success=yes exit=5 a0=15f7520 a1=c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109094): arch=c000003e syscall=87 success=yes exit=0 a0=15f7520 a1=7ffffc461ce0 a2=7ffffc461ce0 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109095): arch=c000003e syscall=87 success=yes exit=0 a0=1616720 a1=7ffffc461ce0 a2=7ffffc461ce0 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079791.931:109096): arch=c000003e syscall=2 success=yes exit=4 a0=1616720 a1=200c2 a2=180 a3=2 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079798.870:109097): arch=c000003e syscall=2 success=yes exit=3 a0=15f9710 a1=241 a2=1a4 a3=7ffffc461920 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
type=SYSCALL msg=audit(1512079798.872:109098): arch=c000003e syscall=87 success=yes exit=0 a0=15f7520 a1=1 a2=1 a3=1 items=2 ppid=18028 pid=21238 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="vi" exe="/usr/bin/vi" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

#### Next, removing the test file "foo" created
```
[root@ip /]# rm /tmp/foo
rm: remove regular file ‘/tmp/foo’? y
```

#### Creates the following log entries. 
```
type=SYSCALL msg=audit(1512079810.810:109106): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=21520c0 a2=0 a3=7fff479577a0 items=2 ppid=18028 pid=21245 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

#### Making the directory "foodir"
```
[root@ip /]# mkdir /tmp/foodir
```
#### Creates the following
```
type=SYSCALL msg=audit(1512079821.330:109107): arch=c000003e syscall=83 success=yes exit=0 a0=7ffe9636c8c3 a1=1ff a2=1ff a3=7ffe9636a980 items=2 ppid=18028 pid=21246 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="mkdir" exe="/usr/bin/mkdir" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```
#### Creating the file foofile in the new directory "foodir" to show that subdirectories will be picked up by the same rule
```
[root@ip /]# touch /tmp/foodir/foofile
```
#### Creates the following
```
type=SYSCALL msg=audit(1512079836.755:109108): arch=c000003e syscall=2 success=yes exit=3 a0=7fffca9448bb a1=941 a2=1b6 a3=7fffca943290 items=2 ppid=18028 pid=21247 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```

#### And lastly removing the file in the subdirectory
```
[root@ip /]# rm /tmp/foodir/foofile
```
#### Creates the following
```
rm: remove regular empty file ‘/tmp/foodir/foofile’? y
type=SYSCALL msg=audit(1512079845.198:109109): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=11090c0 a2=0 a3=7ffc372db250 items=2 ppid=18028 pid=21248 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="files_in_tmp"
```


