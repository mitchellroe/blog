## SELinux Troubleshooting

### Add SELinux fcontext

#### Why?

I wanted to host some web content from a non-standard directory (`/data/www`),
but was getting blocked by SELinux. This is because the file context didn't
match what "web files" should have.

#### How?

Add an SELinux file context (fcontext) definition.

```sh
semanage fcontext -a -t httpd_sys_content_t '/data/www(/.*)?'
```

### Troubleshoot SELinux AVC denials

#### Find the file on which an access attempt is being made

We can search the audit log using `ausearch -m avc`:

    # ausearch -m avc
    type=AVC msg=audit(1597951120.292:3075): avc:  denied  { write } for  pid=41710 comm="php-fpm" name="cache" dev="dm-7" ino=16874562 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:httpd_sys_content_t:s0 tclass=dir permissive=0

There are two pieces of information we need to gather from this output:

1. The device name (`dev="dm-7"`)
2. The inode number (`ino=16874562`)

To find the mount point which corresponds to the device, you can use `lsblk`:

    # lsblk -o kname,mountpoint | grep dm-7
    dm-7  /var

From this output, we can see that `dm-7` is mounted at `/var`. We can then
search `/var` for the inode number we gathered using `find`:

    # find /var -inum 16874562
    /var/www/research.library/site/sp/assets/cache

#### Examples

```
root@kl-web-p01 ~$ ausearch -m avc
type=AVC msg=audit(1597951120.292:3075): avc:  denied  { write } for  pid=41710 comm="php-fpm" name="cache" dev="dm-7" ino=16874562 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:httpd_sys_content_t:s0 tclass=dir permissive=0
root@kl-web-p01 ~$ journalctl -t setroubleshoot --since=today
Aug 20 17:12:32 kl-web-p01.sys.oakland.edu setroubleshoot[59823]: SELinux is preventing php-fpm from write access on the directory cache. For complete SELinux messages run: sealert -l 1d8d6810-6d74-4096-b739-f55a820000aa
root@kl-web-p01 ~$ sealert -l 1d8d6810-6d74-4096-b739-f55a820000aa
SELinux is preventing php-fpm from write access on the directory cache.

*****  Plugin httpd_write_content (92.2 confidence) suggests   ***************

If you want to allow php-fpm to have write access on the cache directory
Then you need to change the label on 'cache'
Do
# semanage fcontext -a -t httpd_sys_rw_content_t 'cache'
# restorecon -v 'cache'

*****  Plugin catchall_boolean (7.83 confidence) suggests   ******************

If you want to unify HTTPD handling of all content files.
Then you must tell SELinux about this by enabling the 'httpd_unified' boolean.

Do
setsebool -P httpd_unified 1

*****  Plugin catchall (1.41 confidence) suggests   **************************


If you believe that php-fpm should be allowed write access on the cache directory by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# ausearch -c 'php-fpm' --raw | audit2allow -M my-phpfpm
# semodule -X 300 -i my-phpfpm.pp


Additional Information:
Source Context                system_u:system_r:httpd_t:s0
Target Context                unconfined_u:object_r:httpd_sys_content_t:s0
Target Objects                cache [ dir ]
Source                        php-fpm
Source Path                   php-fpm
Port                          <Unknown>
Host                          kl-web-p01.sys.oakland.edu
Source RPM Packages           
Target RPM Packages           
Policy RPM                    selinux-policy-3.14.3-41.el8_2.5.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     kl-web-p01.sys.oakland.edu
Platform                      Linux kl-web-p01.sys.oakland.edu
                              4.18.0-193.14.3.el8_2.x86_64 #1 SMP Mon Jul 20
                              15:02:29 UTC 2020 x86_64 x86_64
Alert Count                   49
First Seen                    2020-08-20 10:36:19 EDT
Last Seen                     2020-08-20 17:12:27 EDT
Local ID                      1d8d6810-6d74-4096-b739-f55a820000aa

Raw Audit Messages
type=AVC msg=audit(1597957947.824:3222): avc:  denied  { write } for  pid=3253 comm="php-fpm" name="cache" dev="dm-7" ino=16874562 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:httpd_sys_content_t:s0 tclass=dir permissive=0



Hash: php-fpm,httpd_t,httpd_sys_content_t,dir,write

root@kl-web-p01 ~$ lsblk -o kname,mountpoint | grep dm-7
dm-7  /var
root@kl-web-p01 ~$ find /var -inum 16874562
/var/www/research.library/site/sp/assets/cache
root@kl-web-p01 ~$ setsebool -P httpd_unified 1
```
