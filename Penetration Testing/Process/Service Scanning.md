Port 0 is a **reserved port** in TCP/IP networking and is not used in TCP or UDP messages. If anything attempts to bind to port 0 (such as a service), it will bind to the next available port above port 1,024 because port 0 is treated as a "wild card" port.

## Nmap

### **1. TCP Scan (`-sS`, `-sT`)**

- **How it Works**: Uses the **Three-Way Handshake** (SYN-ACK) to determine open ports.
    
- **Types**:
    
    - **SYN Scan (`-sS`)**: Sends a **SYN** packet and waits for a response:
        
        - **SYN-ACK** → Port is **open** (Nmap resets the connection with an RST)
            
        - **RST** → Port is **closed**
            
        - **No response** → Port is **filtered**
            
    - **Connect Scan (`-sT`)**: Performs a full **three-way handshake**. Less stealthy than SYN scan.
        
- **Advantages**:
    
    - **Fast and efficient** (especially SYN scan).
        
    - **Reliable** for detecting open/closed ports.
        
    - **Commonly used for penetration testing**.
        
- **Disadvantages**:
    
    - SYN scans require **root/admin privileges**.
        
    - Can be detected by **intrusion detection systems (IDS)**.
        

---

### **2. UDP Scan (`-sU`)**

- **How it Works**: Sends **UDP packets** to target ports and waits for a response.
    
    - **ICMP "Port Unreachable"** → Port is **closed**.
        
    - **No response** → Port is **open/filtered**.
        
    - **Application-specific response** → Port is **open**.
        
- **Advantages**:
    
    - **Finds open UDP services** (like DNS, SNMP, DHCP).
        
    - Harder for firewalls to detect compared to TCP scans.
        
- **Disadvantages**:
    
    - **Slower than TCP scans** (UDP has no handshake).
        
    - Many firewalls block UDP traffic, leading to **false positives**.
        
    - Relies on **ICMP error messages**, which some networks block.


### **Common UDP Services & Ports**

| **Service**                                    | **Port**    | **Description**                              |
| ---------------------------------------------- | ----------- | -------------------------------------------- |
| **DNS** (Domain Name System)                   | **53**      | Resolves domain names to IPs.                |
| **DHCP** (Dynamic Host Configuration Protocol) | **67/68**   | Assigns IP addresses dynamically.            |
| **TFTP** (Trivial File Transfer Protocol)      | **69**      | Simple file transfer with no authentication. |
| **SNMP** (Simple Network Management Protocol)  | **161/162** | Used for monitoring and managing devices.    |
| **NTP** (Network Time Protocol)                | **123**     | Synchronizes time between systems.           |
| **RIP** (Routing Information Protocol)         | **520**     | Dynamic routing protocol.                    |
| **Syslog**                                     | **514**     | Logging service for network devices.         |
| **L2TP** (Layer 2 Tunneling Protocol)          | **1701**    | VPN tunneling protocol.                      |
| **IKE** (Internet Key Exchange for IPsec)      | **500**     | Used for setting up VPN connections.         |
| **Quake/CS Game Servers**                      | **27015**   | Online multiplayer gaming servers.           |


Sometimes we will see other ports listed that have a different state, such as `filtered`. This can happen if a firewall is only allowing access to the ports from specific addresses.


- `-sC` parameter to specify that `Nmap` scripts should be used to try and obtain more detailed information
-  The `-sV` parameter instructs `Nmap` to perform a version scan.
- `-p-` tells Nmap that we want to scan all 65,535 TCP ports.

The syntax for running an Nmap script is `nmap --script <script name> -p<port> <host>`.







