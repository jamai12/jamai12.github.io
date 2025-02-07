---
title: "Build Your Own SOCKS Proxy From Scratch"
date: 2025-02-7 00:00:00 +0800
categories: [Red Teaming, Tools, APT Simulation Tooling]
tags: [tunneling, reverse socks proxy, pivoting, network, proxy]
image: /assets/img/tunneling proxy.png
alt: "Reverse Proxy"
---

During offensive engagements, tunneling traffic through a proxy is critical for pivoting within internal networks. The challenge arises when relying on open-source proxies like Chisel, Ligolo, reGeorg, or reSocks, etc tools often flagged by security solutions. Even with obfuscation the advanced EDRs (Endpoint Detection and Response) may still block these tools. 
To fix this, i decided to create my own custom socks proxy, a customized solution not only survives longer in hostile environments but also provides full control over functionality and future upgrades.

**Developing a Custom SOCKS Proxy: My Approach**
1. **Start Simple**: I began by building a basic proxy in Python. This helped me grasp the core structure and logic of tunneling traffic.
2. **Expand to Other Languages**: Using the same principles and logic, i later developed a reverse socks  proxy over HTTPS with C#.

###### **What This Blog Covers**
- How SOCKS proxies work under the hood.
- A step-by-step breakdown of my first SOCKS proxy.

> By understanding proxy mechanics and creating your own tools, you gain flexibility and creativity in red team operations.

## Understanding Proxies and Reverse Proxies
In a simple definition a SOCKS proxy acts as an intermediary between a client and the internet, typically used for anonymity and security.
In offensive security contexts, its primary role shifts to **tunneling traffic** through restricted networks so a reverse SOCKS proxy is designed to tunnel **incoming external traffic** into an internal network, for example, consider machines behind a restrictive firewall with no public IPs. 

![](/assets/img/reverse Socks Proxy.png)

The reverse SOCKS proxy (hosted on a reachable system) accepts connections from the outside and forwards them through the SOCKS protocol to internal targets. This allows:
- Internal servers to remain hidden (no public IP required).
- The proxy to handle all traffic routing, masking internal infrastructure.

> The question here why i used SOCKS?
> While protocols like HTTP/HTTPS can be used for tunneling, SOCKS operates at **Layer 5 (Session Layer)** of the OSI model, making it protocol-agnostic. This means it can handle **any TCP/UDP traffic** (e.g., SSH, RDP, custom protocols) without needing application-layer modifications.

### **Comparing Proxy Protocols**

|Feature|SOCKS Proxy|VPN|HTTP Proxy|
|---|---|---|---|
|**Protocol Support**|Any TCP/UDP|All traffic (Layer 3)|HTTP/HTTPS only|
|**Encryption**|Optional (via SSH)|Always encrypted|Optional (HTTPS)|
|**Performance**|Moderate|Overhead (encryption)|Fast for HTTP|
|**Use Case**|Non-HTTP apps, gaming|Full network privacy|Web browsing|

## Exploring Proxychains
### What is proxychains
**Proxychains** is a powerful open-source tool that forces **any TCP/UDP connection** made by an application to pass through a chain of proxies (e.g., SOCKS, HTTP). It’s widely used for anonymity, bypassing firewalls or penetration testing so let’s break down how it works and explore its **chain types**:
### **How Proxychains Works**
**Interception via LD_PRELOAD**:  
	- Proxychains uses the `LD_PRELOAD` Linux environment variable to **inject itself into the target application** at runtime. This allows it to intercept and reroute network calls (e.g., `connect()`, `sendto()`) through the configured proxy chain.
    - Works with **dynamically linked applications** (not static binaries).
    - No need to modify the application’s code.
 
 **Proxy Chain Configuration**:
    - Proxies are defined in `/etc/proxychains.conf` (or a custom config file).
    - Each proxy in the chain is listed with its type, IP, port, and credentials:
```sh
socks5 192.168.1.10 1080 user1 pass123
http 10.0.0.2 8080
socks4 45.76.12.34 4145
```

**Traffic Routing**:
    - When you run `proxychains <command>` (e.g., `proxychains nmap 192.168.5.10`), the tool:
        1. Reads the proxy chain from the config.
        2. Routes the application’s traffic through each proxy sequentially.
        3. Each proxy in the chain only knows about the **next hop**, enhancing anonymity.
    - Example flow:  
        `Your PC → Proxy1 → Proxy2 → Proxy3 → Target Server`

### **Proxy Chain Types**
###### Dynamic Chain (`dynamic_chain`)
- **Behavior**: Routes traffic through **all proxies in the list**, but **skips dead/unreachable proxies**.
- **Use Case**: Balances reliability and anonymity. Ideal when some proxies might fail.
**Config**:
```sh
chain_type = dynamic_chain
proxy_dns
[ProxyList]
socks5 10.0.0.1 1080
http 192.168.1.5 8080
```

###### **Strict Chain (`strict_chain`)**
- **Behavior**: Traffic passes through **all proxies in the exact order listed**. If one proxy fails, the chain breaks.
- **Use Case**: High-reliability environments where all proxies must be used (e.g., corporate networks).
**Config**:
```sh
chain_type = strict_chain
[ProxyList]
socks5 10.0.0.1 1080
socks5 10.0.0.2 1080
```

###### **Random Chain (`random_chain`)**
- **Behavior**: Traffic passes through a **random subset of proxies** in the list so the length is set with `chain_len`.
- **Use Case**: Maximizing anonymity by randomizing paths (e.g., penetration testing).
**Config**:
```sh
chain_type = random_chain
chain_len = 2  # Use 2 random proxies per request
[ProxyList]
socks5 10.0.0.1 1080
http 192.168.1.5 8080
socks4 45.76.12.34 4145
```


###### **Single Proxy (`proxy_dns direct`)**
- **Behavior**: Uses a **single proxy** (the first in the list). If it fails, traffic goes directly to the target.
- **Use Case**: Simple setups where a backup direct connection is acceptable.
**Config**:
```sh
chain_type = dynamic_chain
proxy_dns
[ProxyList]
socks5 10.0.0.1 1080
```

## Deep Dive into SOCKS Proxies
### Difference Between SOCKS4 and SOCKS5
SOCKS (Socket Secure) is a proxy protocol that routes network packets between a client and a server via a proxy server. SOCKS4 and SOCKS5 are its two main versions, with SOCKS5 being an improvement over SOCKS4.

#### **A- SOCKS4**

| Category | Details |
|----------|----------|
| **Basic Features** | - Supports only **TCP** connections (no UDP support)<br>- Does **not** support authentication (except username-based in some implementations)<br>- **No support for IPv6**, works only with IPv4<br>- **No built-in DNS resolution** – the client must resolve domain names before forwarding |
| **How It Works** | - The client establishes a TCP connection with the proxy<br>- The proxy connects to the target server on behalf of the client<br>- The data is relayed through the proxy |
| **Limitations** | - No support for UDP-based applications (VoIP, gaming, streaming)<br>- No authentication options beyond basic username-based<br>- Less secure and feature-limited compared to SOCKS5 |

#### **B- SOCKS5**

| **Category**                         | **Details**                                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Enhanced Features Over SOCKS4**    | - Supports both **TCP and UDP** traffic.<br>- **Authentication support** (username/password or GSS-API for secure authentication).<br>- **IPv6 support**, making it future-proof.<br>- **Built-in DNS resolution** – the proxy can resolve domain names instead of relying on the client.<br>- **More secure** than SOCKS4 due to authentication options and improved protocol handling. |
| **How It Works**                     | - The client establishes a connection with the proxy.<br>- Authentication is performed (if required).<br>- The proxy establishes a connection to the destination server and can resolve domain names if needed.<br>- Data is relayed securely between the client and the server.                                                                                                         |
| **Advantages of SOCKS5 over SOCKS4** | - Works with **UDP-based applications** (gaming, VoIP, live streaming).<br>- **Faster performance** due to optimized handling of data packets.<br>- **Better security** with authentication and support for encryption layers.<br>- **More flexibility** due to IPv6 and built-in DNS resolution.                                                                                        |

## How reverse Socks proxy work
Now that I understand these concepts, I’ll explain how to create your own SOCKS proxy to truly grasp tunneling through proxies. I often encountered situations where i needed a proxy for pivoting but the biggest challenge was that most proxies were easily detected even after obfuscating and modifying their signatures, they remained difficult to evade advanced EDR solutions and enterprise security measures.

So, i thought why not create my own proxy and fully understand how it works? for that i  started by researching and then jumped straight into developing a simple reverse proxy using Python, i initially used Python because it’s easy to prototype and test ideas and once i had a working version, i applied the same logic and structure to develop a more advanced version in C#, using HTTPS protocol.

The result was a proxy that successfully bypassed multiple security solutions, including Kaspersky Endpoint and Windows Defender Endpoint etc.

Let’s dive in, i w'll start by explaining how a proxy server works.

A proxy server is responsible for handling connections from a client, establishing a persistent socket tunnel and maintaining the connection similar to a reverse shell. The client initiates a connection with the server on a specified port, while the server simultaneously listens for connections from ProxyChains. We can define where ProxyChains should initiate connections in its configuration file located in the `/etc` directory.

Once the server receives a connection from ProxyChains, it forwards it to the previously established tunnel with the client so the client then determines the connection type based on ProxyChains’ request and extracts the target IP address and port from the incoming packet. After extraction, the client creates a socket and initiates a direct connection with the target machine on the specified port, effectively establishing another tunnel within the internal network.

Once the client receives a response from the target machine, it forwards the data back to the proxy server via the persistent socket tunnel. To avoid socket pipe errors, the client closes its direct connection with the target machine but it maintains a connection with the proxy server. The server then forwards the received traffic to ProxyChains without modification, acting as a gateway. Since only the server communicates directly with ProxyChains, it continuously listens for new connections. If no data is received from ProxyChains for a certain period, the server closes the connection and returns to listening mode.

For every new request from ProxyChains on a different port, the server establishes a separate connection. For example, if we use Nmap to scan two different ports like 443 and 445, ProxyChains will first initiate a connection for port 443, retrieve the response from the server, then initiate a new connection for the second port just like in the example below:
![](assets/img/Pasted image 20250207194116.png)


## Proof of Concept (Poc)
the source code is provided in github, so in this scenario we have 3 machines:
	linux machine
	 linux machine
	 windows machine

the first thing we start our proxy server in the port 9090:
![](assets/img/Pasted image 20250207200124.png)

the server in listen mode we need to configure now the proxychains in proxchains configuration file and put the port we want in our case i used 1080
![](Pasted image 20250207200440.png)
 and i should set the same port in proxychains configuration file
![](/assets/img/Pasted image 20250207200602.png)

and than we need to initiate the  connection with the server using client in the second machine that have access to the target machine
![](/assets/img/Pasted image 20250207200836.png)

so once the connection established with the server the sever than will bind in second  port we defined later to intercept proxychains connection to confirm that we can use the following command
![](/assets/img/Pasted image 20250207201113.png)

so than any command we used with proxychains will be forward in the port `1080` for example let's use proxychains with `nxc` command to check smb of the target machine
```sh
proxychains nxc smb 192.168.1.2
```

![](/assets/img/Pasted image 20250207201307.png)

and this is the output from the client 
![](/assets/img/Pasted image 20250207201346.png)

and we can also use for example nmap let's say that we want to scan port 443,445 with scan option `-sCV` 
![](/assets/img/Pasted image 20250207201835.png)

I hope you enjoy reading the blog 😉. Here's the GitHub source code [link](https://github.com/jamai12/Reverse-SOCKS-Proxy.git) for ProxyChains.