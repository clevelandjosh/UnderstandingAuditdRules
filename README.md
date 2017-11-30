# UnderstandingAuditdRules
This is an overview of writing auditd rules for linux


```
grep tmpfiles /var/log/audit/audit.log
type=SERVICE_START msg=audit(1511846972.453:80375): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=SERVICE_STOP msg=audit(1511846972.453:80376): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=SERVICE_START msg=audit(1511933375.086:91082): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=SERVICE_STOP msg=audit(1511933375.086:91083): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=SERVICE_START msg=audit(1512019782.006:101709): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=SERVICE_STOP msg=audit(1512019782.006:101710): pid=1 uid=0 auid=4294967295 ses=4294967295 subj=system_u:system_r:init_t:s0 msg='unit=systemd-tmpfiles-clean comm="systemd" exe="/usr/lib/systemd/systemd" hostname=? addr=? terminal=? res=success'
type=CONFIG_CHANGE msg=audit(1512077955.760:108844): auid=4294967295 ses=4294967295 subj=system_u:system_r:unconfined_service_t:s0 op=add_rule key="tmpfiles" list=4 res=1
type=SYSCALL msg=audit(1512077978.266:108846): arch=c000003e syscall=2 success=yes exit=3 a0=7fffd17b48be a1=941 a2=1b6 a3=7fffd17b2fa0 items=2 ppid=18028 pid=20924 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="tmpfiles"
type=SYSCALL msg=audit(1512078001.433:108854): arch=c000003e syscall=263 success=yes exit=0 a0=ffffffffffffff9c a1=b450c0 a2=0 a3=7ffca425a5a0 items=2 ppid=18028 pid=20925 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="rm" exe="/usr/bin/rm" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="tmpfiles"
type=SYSCALL msg=audit(1512078028.481:108855): arch=c000003e syscall=83 success=yes exit=0 a0=7fff2f7368bb a1=1ff a2=1ff a3=7fff2f734330 items=2 ppid=18028 pid=20932 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="mkdir" exe="/usr/bin/mkdir" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="tmpfiles"
type=SYSCALL msg=audit(1512078058.781:108857): arch=c000003e syscall=2 success=yes exit=3 a0=7ffdc151f8bb a1=941 a2=1b6 a3=7ffdc151ef30 items=2 ppid=18028 pid=20956 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="tmpfiles"
type=SYSCALL msg=audit(1512078282.230:108887): arch=c000003e syscall=2 success=yes exit=3 a0=7ffea5ba38bb a1=941 a2=1b6 a3=7ffea5ba1e90 items=2 ppid=18028 pid=20986 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=9807 comm="touch" exe="/usr/bin/touch" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key="tmpfiles"
```
