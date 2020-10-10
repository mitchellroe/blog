# Docker Containers and DNS

I was having an issue where my docker containers, on a CentOS 8 host, were not
able to use DNS. They were able to talk to IP addresses directly, but they were
unable to utilize my host system's DNS. I found out that Docker is not
officially supported on RHEL 8 as of right now, and that firewalld switched
from using an iptables backend to an nftables backend in RHEL 8. Docker
configures its own access using iptables, but since firewalld is not really
paying attention to iptables, it seems to fail. Switching firewalld to use
iptables again resolved this issue.

`/etc/firewalld/firewalld.conf`:
```
# FirewallBackend
# Selects the firewall backend implementation.
# Choices are:
#       - nftables (default)
#       - iptables (iptables, ip6tables, ebtables and ipset)
#FirewallBackend=nftables
FirewallBackend=iptables
```
