---
layout: post
title:      "The Doman Name System (DNS)"
date:       2019-05-21 19:30:07 +0000
permalink:  the_doman_name_system_dns
---


The global Doman Name System allows us to assign memorable domain names to IP addresses so browsers can load Internet resources. The DNS functions almost like a phonebook by providing a database to be used in the translation of human-readable domain names (`www.nytimes.com`) to IP addresses (`54.204.39.132`) so computers and other devices can access websites.

## **How it Works**
Here's a simplified example of what happens behind-the-scenes with DNS when we try to visit a website using our browser.

1. Type the URL into the browser and press enter (`www.nytimes.com`)
2. The browser client (Chrome, Firefox, etc.) sends the request to the operating system
3. The operating system checks the hosts file `/etc/hosts` to see if the URL is there. If so, the IP address is returned the the browser.
4. If the URL or hostname is not in the hosts file, the URL is sent to the first name server specified in  the resolver configuration file, which configures DNS name servers `/etc/resolv.conf`. If the name server has the IP address for the URL we are looking for cached, it returns the IP address to the browser.
5. If not, the local name server sends the request to a remote name server, which can either be a forwarder or a root name server. A forwarder is just another name server, and a root name server doesn't respond with an IP address, but rather the authoritative name server for that domain. 
6. The local name server sends a query to the authoritative name server, which returns the IP address.
7. The browser uses the IP address to send a request for a web page, which is downloaded to the browser.

One interesting side effect of the name search is that the result will be cached for a period of tiem by the local name server. This means the next time someone in the same network wants to access the same website, the IP address will be stored locally, preventing a remote lookup from happening.

## **DNS Database and Client Configuration**
DNS depends on a database of hostnames and associated IP addresses. The database also defines the style of hostnames used on the internet, called a FQDN (Fully Qualified Domain Name). FQDNs have 3 parts:

1. The Top Level Domain Name (TLDN), such as .com, .net, .org, .edu and others. All of these TLDNs are managed on the root name servers.
2. The second level domain is immediatedly to the left of the TLDN, such as google.com, nytimes.com, nyu.edu. These represent the organizational address portion of the FQDN.
3. The third level is the hostname portion, for example host1.example.com. or www.gmail.com

![](https://opensource.com/sites/default/files/images/business-uploads/wg61vm.png)
This image showed the DNS database hierarchy.

Most computerts need some configuration in order to access name services. Typically, this is performed at boot time in computers that are configured using Dynamic Host Configuration Protocol (DHCP). There are ways to customize your configuration and override defaults, but I won't get into that too much here.

## **DNS Record Types**
There are a lot of DNS Record Types. I'll talk about some of the more common ones I've seen in the wild (while developing my band website: [www.thegraspingstraws.com](www.thegraspingstraws.com)).

* **SOA** is the Start of Authority record. It contans the name of the server for the zone, and the zone administrator, a serial number that represents the first version of the file and increments sequentially with any changes, and various times that secondary servers should perform a refresh, and wait for retries if the first refresh fails.
* **$ORIGIN** is like a variable assignment. Its value can be any name in an A or PTR record that does not end in a period. The @ symbol is used as a shortcut for this variable.
* **NS** specifies the authoritative name server for the zone. This record will usually point to the local host.
* **A** is the address record type that specifies the relationship between the host name and the IP address assigned to the host.  This is the most common type of DNS database record.
* **CNAME** is an alias for the name in the A record for a host. Where an A record points to an IP address, a CNAME can point to another domain.


To read more about DNS, visit [this article](https://opensource.com/article/17/4/introduction-domain-name-system-dns). Thanks for reading! I hope this was helpful.

![](https://media.giphy.com/media/RxR1KghIie2iI/giphy.gif)





