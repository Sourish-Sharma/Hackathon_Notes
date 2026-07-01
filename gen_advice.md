- always try manually typing robots.txt or .git

- For *ROT13* => `echo "Hello World" | tr 'A-Za-z' 'N-ZA-Mn-za-m'` where we are translating from the second format to the first format

- For Hex to text => `echo "5069636f435446" | xxd -r -p` where -r reverses a hex dump back into binary/text and -p tells it to read plain cont hex or you can use python `python3 -c "print(bytes.fromhex('5069636f435446').decode('utf-8'))"` and to convert from text to hex `echo -n "PicoCTF" | xxd -p`. where -n on echo tells it to avoid adding a newline at the end of it

- For base64, you can use `echo -n "secret_key" | base64 # Output: c2VjcmV0X2tleQ==` for encoding and `echo "c2VjcmV0X2tleQ==" | base64 -d # Output: secret_key` for decoding

- If you forget the name of a Linux command entirely, use apropos. It searches through the short descriptions of all offline manual pages for a keyword 

```Bash
apropos "md5"
apropos "partition"
apropos "decode"
```

- For converting hash to plain text, in Kali there is a rockyou.txt wordlist at `/usr/share/wordlists/`, which you can use to convert with tools like hashmap or john (john the ripper)

- in JSON files, do not put the , at the end if there is no other element

- in python, I can open python in a terminal and use help() to find what something does, and import something then ask help() for it to learn on the go. Also if allowed I can use `pydoc3 <command>` to check what it does like a man page. I can also use `dir(object)` to check for all the methods available on it 

- If you know your output is running of the screen, pipe it to less `cat bird.jpg | less`

- Always use `file target.dat` and `exiftool target.dat` first in forensics questions

- To check if the flag is directly put into the file, you can use strings (always use this anyways) with `strings file.dat | grep "flag"`

- for files, also you have to use `xxd file.type | less` is forensics, here xxd takes a binary file and converts it to a standard human readable hex dump

- binwalk is another very important tool for analyzing and forensics and finding if anything is hidden in another file, you should use `binwalk -e target.dat`