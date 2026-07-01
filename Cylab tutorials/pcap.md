## pcap

- *#1: pcap poisoning*: Had to check solution, the guy found it after manually examining all the packets, but a better solution was `strings trace.pcap | grep "pico"` which instantly gave the solution, so remember this