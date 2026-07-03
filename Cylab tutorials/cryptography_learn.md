- *StegoRSA*
    - Used exiftool to get the hex code
    - Converted the hex into key, which was then saved in a .pec file
    - used openssl this command: `openssl rsautl -decrypt -inkey pico.pem -in flag.enc` where rsautl tells openssl to use rsa utility, -decrypt sets the mode, -inkey defines the next argument would be the source of the key, -in defines the exact input file to be decrypted
    /# should use `pkeyutl` instead of `rsautl` as it is newer

- Here’s a brief version of the RSA algorithm: 
1. Privately select two large prime numbers, P and Q. If someone gains access to
these, then you are vulnerable to attack.
2. Multiply the two numbers to create n = P x Q. This is your public key.
3. Calculate Φ(n) such that Φ(n) = (P - 1) x ( Q - 1).
4. Choose a number, e, such that 1 < e < Φ(n).
5. Your total public key is (n, e).
6. Calculate d = (k*Φ(n) + 1)/e for some integer k. d is your private key!
7. Your total private key is (n,d).