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


###4 SOA
```
$TTL 86400
$ORIGIN example.com.
@ IN SOA ns1.example.com. hostmaster.example.com.(
  2015010101 ; serial yyyymmddss
  21600 ;refresh after 6 hours
  604000 ; expire after 1 week
  86400 ) ; nxdomain TTL of 1 day
```
  
  
###5 NS
syntax:
- name ttl class NS name
```
$TTL 86400
$ORIGIN example.com.
@ IN SOA ns1.example.com. hostmaster.example.com.(
  2015010101 ; serial yyyymmddss
  21600 ;refresh after 6 hours
  604000 ; expire after 1 week
  86400 ) ; nxdomain TTL of 1 day
  IN NS ns1.example.com.
  IN NS ns2.example.com.
```

###6 MX
MX Resource Record
- mail exchanger
- name ttl class MX preference name
- Tries the MX with the lowest preference first (if multiple mail servers have same priority, choose randomly)
```
$TTL 86400
$ORIGIN example.com.
@ IN SOA ns1.example.com. hostmaster.example.com.(
  2015010101 ; serial yyyymmddss
  21600 ;refresh after 6 hours
  604000 ; expire after 1 week
  86400 ) ; nxdomain TTL of 1 day
  IN NS ns1.example.com.
  IN NS ns2.example.com.
  IN MX 10 mail.example.com.
```
###7 A
```
server1 IN A 10.0.8.6
server2 IN A 10.9.8.4
```

###8 CNAME
name ttl class CNAME name
```
ftp IN CNAME server1
www IN CNAME server2
voip IN CNAME voip.example.net.
```
avoid chaining CNAMEs


###9 PTR
Pointer Resource Record
- Used for reverse lookups
- Forward lookup



##4. Basic Name Server Setup
###2 Create and verify the zone file
ke.zone file
```
$ORIGIN ke.com.
$TTL 3M ;3 minutes
@ IN SOA ns1.ke.com. hostmaster.ke.com. 2017010101 3M 3M 3M 3M
  IN NS ns1.ke.com.
ns1 IN A 192.168.0.100
```
vim /etc/named/conf
```
zone "ke.com" IN{
  type master;
  file "ke.zone";
};
```
verify check
```
named-checkconf /etc/named.conf
named-checkconf -p /etc/named.conf //verbose
named-checkzone ke.site ke.zone
```

##5. Advenced Name Server Setup
```
buddyns.com
