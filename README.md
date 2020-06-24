iptables ansible role

You need to set config for each host in host_vars ( host_vars/ip.yml ) or in group_vars ( group_vars/group.yml )

policies:
You need to describe POLICY for each of chains ( standard chains )

rules:
src - source ( IP, subnet, dns )
dst - destination ( IP, subnet, dns )
proto - protocol ( tcp, udp, icmp )
ports - ports with comma delimiter ( role uses multiport module )
action - action for rule ( ACCEPT, DROP, etc )
comment - comment for rule ( string )

config example:
```
iptables:
 policies:
  filter:
   input: "ACCEPT"
  nat:
   postrouting: "ACCEPT"
 rules:
  nat:
   postrouting:
    - { action: "MASQUERADE", comment: "for docker" }
  filter:
   input:
    - { src: "1.1.1.1", proto: "tcp", action: "ACCEPT", comment: "monitoring server" }
    - { src: "1.1.1.1/24",  proto: "tcp", action: "ACCEPT", comment: "docker net" }
    - { src: "dns.me", proto: "tcp", action: "ACCEPT", comment: "infra server" }
    - { ports: "8080,8081,8888", proto: "tcp", action: "DROP", comment: "hide ports" }
```

validate with:
```
iptables-restore --test %s
```
