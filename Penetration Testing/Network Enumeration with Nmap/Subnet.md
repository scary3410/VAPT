## 🌐 What is a Subnet?

A **subnet** (short for **subnetwork**) is a smaller network **within a larger network**. Subnetting allows you to divide a large IP network into multiple **logical segments**.

---

## 📦 IP Address Recap

An IP address looks like this:

CopyEdit

`192.168.1.10`

It consists of two parts:

- **Network part**: Identifies the **network**.
    
- **Host part**: Identifies a **specific device** in that network.
    

---

## 📏 CIDR Notation

CIDR (Classless Inter-Domain Routing) notation is used to represent **subnets**:

CopyEdit

`192.168.1.0/24`

Here:

- `192.168.1.0` is the **network address**.
    
- `/24` means **the first 24 bits are the network portion**, the remaining bits (8 in this case) are for hosts.
    

---

## 📐 Subnet Sizes Table

|CIDR|# Host Bits|# Hosts (usable)|Subnet Mask|Example Range|
|---|---|---|---|---|
|/30|2|2|255.255.255.252|192.168.1.0 - 192.168.1.3|
|/29|3|6|255.255.255.248|192.168.1.0 - 192.168.1.7|
|/28|4|14|255.255.255.240|192.168.1.0 - 192.168.1.15|
|/24|8|254|255.255.255.0|192.168.1.0 - 192.168.1.255|
|/16|16|65,534|255.255.0.0|192.168.0.0 - 192.168.255.255|
|/8|24|16,777,214|255.0.0.0|10.0.0.0 - 10.255.255.255|

🧠 Note: "Usable hosts" excludes **network address** and **broadcast address**.

---

## 🎯 Examples:

### ✅ `10.129.2.0/24`

- This means:
    
    - Network: `10.129.2.0`
        
    - Subnet mask: `255.255.255.0`
        
    - Host range: `10.129.2.1` to `10.129.2.254`
        
    - Broadcast: `10.129.2.255`
        
    - Total usable IPs: **254**
        

---

## 🛠️ Why Subnet?

- **Efficient IP usage**: Avoid wasting IPs.
    
- **Security**: Separate networks (e.g., guest Wi-Fi vs internal LAN).
    
- **Performance**: Smaller networks reduce broadcast traffic.
    
- **Routing control**: Easier to manage with routers.
    

---

## 🔎 Subnetting in Hacking / Pentesting

- Helps identify the full range of target IPs.
    
- You may scan a whole subnet using Nmap, like:
    
    bash
    
    CopyEdit
    
    `nmap -sn 10.10.0.0/16`
    
- Knowing how many hosts you can expect lets you:
    
    - Guess the internal structure.
        
    - Prioritize scanning.
        
    - Avoid detection by limiting the scan scope.
        

---

## 🧮 How to Calculate Subnet Ranges?

Say you have `192.168.1.0/27`:

- 27 bits = 255.255.255.224
    
- 32 total IPs per subnet (2^5)
    
- 30 usable hosts per subnet
    
- Subnets would be:
    
    - 192.168.1.0 - 192.168.1.31
        
    - 192.168.1.32 - 192.168.1.63
        
    - etc.

If we use the same scanning technique on the predefined list, the command will look like this:

  Host Discovery

```shell-session
0xscary@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

|**Scanning Options**|**Description**|
|---|---|
|`-sn`|Disables port scanning.|
|`-oA tnet`|Stores the results in all formats starting with the name 'tnet'.|
|`-iL`|Performs defined scans against targets in provided 'hosts.lst' list.|

In this example, we see that only 3 of 7 hosts are active. Remember, this may mean that the other hosts ignore the default **ICMP echo requests** because of their firewall configurations. Since `Nmap` does not receive a response, it marks those hosts as inactive.