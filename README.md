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
/etc/named.conf
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


####01:43
Downsides
- Same as caching name server
- May return stale results

Benefits
- Reduced external network traffic
- Adds a layer of abstraction
- Lightens the load on the name server  



/etc/named.conf
```
recursion yes -> no;
```
add
```
forward only;
forwards{8.8.8.8;8.8.4.4;}
```

###3 Authoritative-only name server
edit
```
view "external"{
  zone "my.external.zone"{
    type master;
    file "my.external.zone.db";
  }
}
```
###4 Authoritative-only name servers

##3. Zone Configuration File
###1 Zone config files
###2 $TTL
$TTL
- sets the default TTL for the zone
- can be overridden
- value in seconds
- maximum about 68 years
- 2400000 seconds ~=4weeks

####01:09 syntax
```
$TTL 172800
```


###3 $ORIGIN
```
$ ORIGIN example.com.
www IN A 192.168.1.45
www.example.com.
ftp IN CNAME server1
ftp.example.com. IN CNAME server1.example.com.
```

IN FQDN
```
ftp.example.org.
```
Is not FQDN
```
ftp.example.org //unqualified
```
