# dns_ns_error_rescue
rescue proposal for domain ns error rescue like 2010.01.12 baidu ns accident

# Introduction

[Baidu DNS records hijacked accident in 2010](http://www.zdnet.com/article/baidu-dns-records-hijacked-by-iranian-cyber-army/) is a domain ns accident from Registrar.

Many tasks can be done for this accident: Registrar should build a more secure system. The victim domain owner should contact registrar for rescue. The recursive resolver is best to flush ns record in 1~2 days even the ns ttl is large than 1 week or longer.

But in the accident time window, there can be some temporary rescue proposal to Mitigate the influnence, such as:

[Patents Application: Mitigating Domain NS record hijack influnence by moving NS](https://www.google.com/patents/CN106209832A)

[Patents Application: Mitigating Domain NS record hijack influnence by layering NS](https://www.google.com/patents/CN106210165A) 

# Overview

For example, foo.com's glue ns is hijacked at registrar like baidu.com accident of 2010.

correct ns: ns1.foo.com; ns2.foo.com

evil ns: ns1.evil.com; ns2.evil.com

Assume that foo.com's ns cache is not expired in some recursive resolver (RECUR-X), the ns ttl is still 60 seconds left.

RECUR-X will query hot subdomains like "www.foo.com" to the old correct ns[12].foo.com server. 

We can adjust to enlarge the expire ttl for these hot subdomains on this "not expired ns" RECUR-X.

## Mitigating Domain NS record hijack influnence by moving NS

ns[12].foo.com move hot subdomain's ns record, www.foo.com's NS is temporary set to ns.bar.net.

In the accident time window, www.foo.com will not be affect for 259200 seconds on RECUR-X, not the ns cache ttl's 60 seconds.

    ;; QUESTION SECTION:
    ;www.foo.com.                    IN      A

    ;; AUTHORITY SECTION:
    www.foo.com.   259200    IN      NS      ns.bar.net.

    ;; ADDITIONAL SECTION:
    ns.bar.net.  259200     IN      A       111.111.111.111


## Mitigating Domain NS record hijack influnence by layering NS

ns[12].foo.com layering hot subdomain's resolution, www.foo.com is temporary CNAME to www.bar.net, and bat.net's ns is ns.bar.net.

In the accident time window, www.foo.com will not be affect for 259200 seconds on RECUR-X, not the ns cache ttl's 60 seconds.


    ;; QUESTION SECTION:
    ;www.foo.com.                    IN      A

    ;; ANSWER SECTION:
    www.foo.com.    259200     IN    CNAME   www.bar.net.

    ;; AUTHORITY SECTION:
    bar.net.   259200    IN      NS      ns.bar.net.

    ;; ADDITIONAL SECTION:
    ns.bar.net.  259200     IN      A       111.111.111.111

# What about directly enlarge hotdomain's A record ttl

If directly enlarge hotdomain's A record ttl, there will not space for CDN's dynamic domain traffic redirect optimization, not flexible.

    www.foo.com.    259200     IN    A   xxx.xxx.xxx.xxx
