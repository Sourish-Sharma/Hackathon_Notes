## Network Security Tools

### nmap
*The tool to scan an IP and find out what services are running on it*

- Command to memorize: `nmap -sV -sC -T4 <TARGET_IP>` where:
    - `-sV` : probes open ports to tell you exactly what software are running
    - `-sC` : Runs default enumeration scripts and looks for easy vulnerabilities automatically
    - `-T4` : makes the scan run fast/ timing template
    - we can also add `-p-` if you want it to scan all the ports, without it only the top 1000 ports are scanned

### Netcat (nc)
*nc reads and writes data across network connections using TCP or UDP, mostly used in hackathons for challenge ports or to catch a shell*

- Commands to memorize: `nc <IP> <PORT>` to connect to a challenge and `nc -lvnp <PORT>` to listen to catch data where:
    - `-l` listen mode
    - `-v` verbose mode
    - `-n` forces it to look at raw numerical ip, avoiding domain lookups
    - `-p` dictates exact port number I want to open my machine to for the incoming payload

    - Also run this inside the shel when listening to upgrade it to an interactive shell: 
    ```Python
    # Run this inside your newly caught Netcat shell to upgrade it to an interactive TTY shell:
    python3 -c 'import pty; pty.spawn("/bin/bash")'
    ```

### Wireshark

*wireshark captures traffic on a network interface. In a hackathon, it will be used to read and analyze .pcap files*

- always use strings with pcap files, if small just directly do `strings xyz.pcap`, or if big you can try `strings xyz.pcap | grep "flag"` or something like that, if you can't find it maybe manually search for it

Apart from this, there are some more plays possible here:
    - 1) Data Exfiltration via ICMP (Ping) or DNS: Go to the statistics then conversation and look out for the big files and follow them

    - 2) Transport Layer Security (TLS) Decryption: data will be encrypted, you need to dycrypt it with the key already provided by putting it in by following this: **Inside Wireshark, go to Edit $\rightarrow$ Preferences $\rightarrow$ Protocols $\rightarrow$ TLS. Look for the field labeled (Pre)-Master-Secret log filename, browse to select the provided text key file, and click Apply**

    -3) if it follows something like TFTP or related, it could have files in it so you should go to export object and pick the correct type to get the files on your system for further process  