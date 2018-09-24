# PGP good practice part2
- Creating keys offline, with proper gpg.conf file. 
- Creating sub-keys for signing (messages), encrypting, and authenticating. A master key will have certify capability (signing others key). 
- Save the master key away from any online PC. 
- Create also revoke certificates and save them with the master secret key in a secure and save place. 

This part will cover:
## Generating keys offline
1- A good practice is to generate gpg offline using live CD (e.g. Tails), in a host with all WiFi, ethernet, Bluetooth etc OFF. 
2- Before going a head make sure your gpg.conf file is properly configured as shown in the previous part. 
3- Create subkeys to be used day to day, and save the master key away. 

### Generate master key with certify capability only. 
For purpose of testing we will create new directory 'gnupg-test' and create 'gpg.conf' file inside it with the proper configuration as in the previous part. 
```
gpg --homedir ./gnupg-test --expert --gen-key
```
Expert option will give us ability to select what type of capabilities we want our key to have. 
Propet will show list of options select **8** to be able to choose key capabilities. 
```
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 8
```
By default key will have Sign, Certify and Encrypt cababilities, we need to untoggle (S sign,E encrypt) and only keep Certify. So select S to untoggle sign.
```
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Sign Certify Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection?  S
```
Then select E, to untoggle encrypt
```
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Certify Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? E
```
Now you can see only 'Certify' capability is there, select Q to finish. 
```
Possible actions for a RSA key: Sign Certify Encrypt Authenticate 
Current allowed actions: Certify 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
```
Then select key length 
```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
```
Select expirey date. NEVER select 0 (if key gets compromised then it will never die), always set expiry date (don't warry you can extend that).
```
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
```
Rest is your name and email, don't add any comments (don't leak any info about your key). 
Then select passphrase to encrypt your keyfile itself. 


