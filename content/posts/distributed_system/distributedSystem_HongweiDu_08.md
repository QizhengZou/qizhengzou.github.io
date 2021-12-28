---
title: "DistributedSystem_HongweiDu_08"
date: 2021-12-03T14:36:19+08:00
lastmod: 2021-12-24
tags: [distributed_system]
categories: [School Courses]
slug: Name Services
draft: true
---
# Name Services 命名服务

Name, address, route：
- The name indicates what we seek
    - Human readable
    - Universal Resource Names (URNs)
- An address indicates where it is
    - IP address, port
- A route tells how to get there 
    - Internet routing

Name and IP-address Not 1 to 1 Mapping：
- One host may map to more than one name 
    - One server machine may be the web server (www.foo.com), mail server (mail.foo.com), etc.  
- One host may have more than one IP address
    - IP addresses are per network interface
    - A machine may have multiple network interfaces (e.g., a gateway)
- Names don’t necessarily reflect geographical locations

Name Hierarchy：
- Naming in Internet is Hierarchical
    - Better scalability (decreasing centralization)
    - Better name space management
- Example: weather.yahoo.com belongs to yahoo.com which belongs to .com
    - Regulated by global non-profit organizations
- First,  get a domain name; then you are free to assign subnames in that domain
    - How to get a domain name (see later)

Top-level Domains：
- Country Code Domains 
    - .uk, .de, .jp, .us, .tv,…
- Generic Domains 
    - .aero, .biz, .com, .coop, .edu, .gov, .info,  .int, .mil, .museum, .name, .net, .org, and .pro 
- Infrastructure Domain (Address and Routing Parameter Area domain)
    - .arpa

How to get a domain name?
- The Internet Corporation for Assigned Names and Numbers (ICANN) is an internationally organized, non-profit corporation that has responsibility for 
    - generic and country code Top-Level Domain name system management,
    - IP address space allocation, 
    - protocol identifier assignment, and 
    - root server system management functions
- ICANN authorizes other companies to register domains

Name Services:
- What is the name service: A name server maintains a database of bindings between human-readable names and attributes of objects (locations, addresses, etc).
- Why difficult: In open distributed systems, name database is distributed and managed by different servers, and those servers are required to cooperate to resolve names. 
- Requirements: 
    - openness
    - scalability
    - fault tolerance (availability)

DNS: Domain Name System:
- Distributed database implemented in hierarchy of many name servers
- DNS services:
    - host name resolution
    - mail host location (e.g., find the mail server for hwdu@hitsz.edu.cn)
    - reverse resolution
    - well-known services (e.g., telnet, FTP, HTTP, etc)
- The add/delete of a name is done by an authoritative administrator manually editing the name database.

DNS Name Servers：





