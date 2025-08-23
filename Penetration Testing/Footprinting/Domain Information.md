
We can also output the results in JSON format.

#### Certificate Transparency

  Domain Information

```shell-session
0xscary@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
```

If needed, we can also have them filtered by the unique subdomains.

  Domain Information

one liners



```shell-session
0xscary@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
```

```shell-session
0xscary@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Once we see which hosts can be investigated further, we can generate a list of IP addresses with a minor adjustment to the `cut` command
### **What it does, step by step**:

1. **`for i in $(cat subdomainlist)`**  
    Reads each line (i.e., each subdomain) from the file `subdomainlist` and assigns it to variable `i`.
    
2. **`host $i`**  
    Runs the `host` command on that subdomain — this performs a DNS lookup to resolve the IP address(es) for the domain.
    
3. **`grep "has address"`**  
    Filters only the lines that indicate an A record (IPv4 address), which usually looks like:
    
    `sub.example.com has address 192.168.1.1`
    
4. **`grep inlanefreight.com`**  
    Further filters only those lines which **contain the string `inlanefreight.com`** — this is strange because IP addresses don’t contain domain names.  
    This will match **only if** the **subdomain itself contains `inlanefreight.com`**.
    
5. **`cut -d" " -f1,4`**  
    Splits the filtered line by spaces and prints **field 1** (domain name) and **field 4** (IP address).




```shell-session
0xscary@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
0xscary@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done
```

### **What it does**:

1. **Reads each IP** from the `ip-addresses.txt` file.
    
2. **Runs `shodan host $i`**  
    Queries Shodan to get detailed information about the services, open ports, and metadata of each IP.


### DIG


```shell-session
0xscary@htb[/htb]$ dig any <Target>
```


The `ANY` query asks the DNS server to return **all types of records** it has for that domain. These can include:

- **A**: IPv4 address
    
- **MX**: Mail exchange servers
    
- **NS**: Name servers
    
- **TXT**: Misc text records (e.g. SPF, verification keys)
    
- **SOA**: Start of Authority (authoritative info about the domain)


