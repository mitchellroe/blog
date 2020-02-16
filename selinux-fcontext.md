## Add SELinux fcontext

### Why?

I wanted to host some web content from a non-standard directory (`/data/www`),
but was getting blocked by SELinux. This is because the file context didn't
match what "web files" should have.

### How?

Add an SELinux file context (fcontext) definition.

```
semanage fcontext -a -t httpd_sys_content_t '/data/www(/.*)?'
```

[home](README.md)
