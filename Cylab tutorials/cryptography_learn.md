- *StegoRSA*
    - Used exiftool to get the hex code
    - Converted the hex into key, which was then saved in a .pec file
    - used openssl this command: `openssl rsautl -decrypt -inkey pico.pem -in flag.enc` where rsautl tells openssl to use rsa utility, -decrypt sets the mode, -inkey defines the next argument would be the source of the key, -in defines the exact input file to be decrypted
    /# should use `pkeyutl` instead of `rsautl` as it is newer

- 