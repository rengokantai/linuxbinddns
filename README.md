# linuxbinddns
##1. Installing BIND
###1 Installing BIND
centos
```
yum install bind bind-utils
```
ubuntu
```
apt install bind9 bind9utils bind9-doc
```


##2. Types of Name Servers
###1 Caching name server
```
/etc/resolv.conf
```
named.conf
```
acl internals{172.16.72.0/24; 192.168.1.0/24;};
options{
  recursion yes;
  allow-recursion{internals;};
};
zone "." IN{
  type hint;
  file "root.servers";
};
```


###2 Forwarding name server
Caching NS
- Caches results of DNS queries
- Conducts __recursive__ queries
- Sends __multiple__ queries and receives multiple replies

Forwarding NS
- Caches results of DNS queries
- Conducts __nonrecursive__ queries
- Sends a __single__ query and receives a single reply
