- always try manually typing robots.txt or .git

- For *ROT13* => `echo "Hello World" | tr 'A-Za-z' 'N-ZA-Mn-za-m'` where we are translating from the second format to the first format

- For Hex to text => `echo "5069636f435446" | xxd -r -p` where -r reverses a hex dump back into binary/text and -p tells it to read plain cont hex or you can use python `python3 -c "print(bytes.fromhex('5069636f435446').decode('utf-8'))"` and to convert from text to hex `echo -n "PicoCTF" | xxd -p`. where -n on echo tells it to avoid adding a newline at the end of it