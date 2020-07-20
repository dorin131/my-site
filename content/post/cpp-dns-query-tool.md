---
title: "C++: Building a DNS query tool - What I learned"
draft: false
date: 2020-07-19T22:00:00Z
slug: "cpp-dns-query-tool"
tags: ["c++", "dns", "network", "udp"]
---
### Preface
So I made this CLI tool which lets you query for a domain name and it returns one or more IPs.
It's pretty much just an interface for your DNS server (8.8.8.8, 1.1.1.1, etc.).

Check it out here: https://github.com/dorin131/dns-query

It only has your standard libraries which are probably included with your OS (Linux), so compiling it should not be a challenge.

You would tell it:
```bash
./dns fodor.org
```

And you should get back something like:
```bash
DNS Server: 1.1.1.1

- Query
Name: fodor.org
Answers RRs: 4
Authority RRs: 0
Additional RRs: 0

- Answer(1)
Type: 1
Class: 1
TTL: 300
Data length: 4
Address: 185.199.110.153

- Answer(2)
Type: 1
Class: 1
TTL: 300
Data length: 4
Address: 185.199.109.153

- Answer(3)
Type: 1
Class: 1
TTL: 300
Data length: 4
Address: 185.199.108.153

- Answer(4)
Type: 1
Class: 1
TTL: 300
Data length: 4
Address: 185.199.111.153
```

It is nothing complicated, but me being a newbie at C++ and networking, I learned a few interesting things along the way.

### Learnings

#### 1. Wireshark is your friend

How do you even start making a DNS tool, right? Well, you first have to see what what kind of communication happens between your computer and a DNS server and then try to "reverse engineer" that.
Well, you don't really need to reverse engineer anything because the domain system and protocol are standardised and available publicly (https://tools.ietf.org/html/rfc1035).

Wireshark will let you inspect all your network traffic and you can clearly see what kind of data is exchanged between you and the DNS server.

![Wireshark](/img/cpp-dns-query-tool/dns-wireshark.png)

#### 2. You talk to DNS servers through UDP at port 53

The extremely quick requests to the DNS servers are done over the User Datagram Protocol. The structure of datagram is very simple: a lightweight header, which has the source and destination ports, length and checksum; and a data section which holds the contents that we want to communicate.

#### 3. UDP requests are easy in C/C++

All you need to know are three functions: `socket`, `sendto` and `rcvfrom`. That's it.

You will first create a socket connection and then send/receive the data.

#### 4. Getting the data structure right is the hardest part

The DNS server is expecting to receive the request in a specific format and will reply back with more data which you have to make sense of.

Fortunately the message format is documented in RFC1035 which describes the details of the domain system and protocol.

As a developer, you will have to translate the specs in the document to a "struct", which your program will use to handle your outgoing and incoming data. Otherwise you only have an array of bytes that don't make much sense.

#### 5. You'll be casting a lot

This point is connected to the one above. You will get unstructured data from the server and will have to cast it to the types that you have created based on the available documentation.

Working with a programming language like C++, you have lots of power and flexibility when it comes to handling your data. If you tell it that at address 0xdeadbeef there is an IP address, it will take your word for it. Hence you have to proceed carefully and make sure that "structs" you have created and the types you have casted are 100% correct. Mistakes will not be tolerated.

The are at least 5 ways to cast in C++. Here is a brief description of these: https://www.tutorialspoint.com/Regular-cast-vs-static-cast-vs-dynamic-cast-in-Cplusplus

#### 6. Endianness will catch you off-guard (at least it did me)

So we've got bytes, which are typically 8 bits long. Then we can compose multiple bytes to represent larger types, like 16 bit, 32 bit or 64 bit integers. Now, to string together these bytes to get a multibyte integer can be done in two directions, and different CPUs actually do it differently ¯\_(ツ)_/¯.

What we have is "big-endian" and "little-endian" machines. The "Network Byte Order" though is big-endian, so whatever machine you are using, when transmitting or receiving your data over internet you have to transform it to/from network byte order. This is to prevent conflicts of order between different types of machines.

A nicer explanation can be found here: https://www.gta.ufrj.br/ensino/eel878/sockets/htonsman.html

### Wrap up

I encourage you to fork, modify or improve the DNS tool I wrote https://github.com/dorin131/dns-query.