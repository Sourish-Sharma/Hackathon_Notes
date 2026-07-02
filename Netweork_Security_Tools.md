## Network Security Tools

### nmap
*The tool to scan an IP and find out what services are running on it*

- Command to memorize: `nmap -sV -sC -T4 <TARGET_IP>` where:
    - `-sV` : probes open ports to tell you exactly what software are running
    - `-sC` : Runs default enumeration scripts and looks for easy vulnerabilities automatically
    - `-T4` : makes the scan run fast/ timing template
    - we can also add `-p-` if you want it to scan all the ports, without it only the top 1000 ports are scanned

### Netcat (nc)
*nc reads and writes data across network connections using TCP or IDP, mostly used in hackathons for challenge ports or to catch a shell*

- Commands to memorize: `nc <IP> <PORT>` to connect to a challenge and `nc -lvnp <PORT>` to listen to catch data

### Wireshark

*wireshark captures traffic on a network interface. In a hackathon, it will be used to read and analyze .pcap files*

- always use strings with pcap files, if small just directly do `strings xyz.pcap`, or if big you can try `strings xyz.pcap | grep "flag"` or something like that, if you can't find it maybe manually search for it

Apart from this, there are 2 more plays possible here:
    - 1) Data Exfiltration via ICMP (Ping) or DNS:

    - 2) Transport Layer Security (TLS) Decryption: data will be encrypted, you need to dycrypt it with the key already provided by putting it in by following this: **Inside Wireshark, go to Edit $\rightarrow$ Preferences $\rightarrow$ Protocols $\rightarrow$ TLS. Look for the field labeled (Pre)-Master-Secret log filename, browse to select the provided text key file, and click Apply** 