## pcap

- *#1: pcap poisoning*: Had to check solution, the guy found it after manually examining all the packets, but a better solution was `strings trace.pcap | grep "pico"` which instantly gave the solution, so remember this

- #2: Wireshark doo dooo do doo...: 
    - opened whiteshark after trying string, and gave up and went with solution
    - So you can go to statistics -> conversation to check the size of TCP, as TCL is encrypted anyways and try to find the flag there
    - Since it was a rot13, just searching for `{` with ctrl+f also worked 

- #3: Trivial Flag Transfer Protocol
    - Nightmare, went straight to solution
    - So firstly since it was TFTP, remember they have files, so downloaded it with Export object -> TFTP and got shit in it
    - Now I did a rot13 on plan to get the passwd, then `dkpg -c program.deb` to see what all is inside, where I found steghide and then ran `steghide extract -sf pictureX.bmp` until and unless I found which image it worked on and it generated the flat.txt