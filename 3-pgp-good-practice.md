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

Note: we are working with asymatric crypto, that means always keys are pair. If we create master key we will end up haveing one public and one private both are master keys. Similar with sub keys there will be one public and one private for each created subkey. Each pair have same fingerprint. The key that hold all this chain is the master private key.

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

### Generate sign/encrypt/authenticate subkey
Next we will add three subkeys one for sign, encrypt and authenticate. They are all connected and drived by the master key. Master key can revoke any subkeys, subkeys can't revoke master key. That's way we need to keep the master key away, and only use the subkeys. 

Open master key for editing, then type 'addkey', provide master key passphrase. 
```
gpg --homedir ./gnupg-test2 --expert --edit-key <keyID>
```
1- Sign subkey
```
>addkey
```
Select 4 for singing only. 
```
You need a passphrase to unlock the secret key for
user: "alice <alice@example.com>"
1024-bit RSA key, ID 0xDCC9E9EDBA6103EB, created 2018-09-24

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 4
```
Then select 'key length', 'expire date', as we did with master key. 
You should end up with one one subkey (usage S) under the existing master key (usage C) similar to:
```
pub  1024R/0xDCC9E9EDBA6103EB  created: 2018-09-24  expires: 2020-09-23  usage: C   
                               trust: ultimate      validity: ultimate
sub  1024R/0x690C42EEC8BD1C9B  created: 2018-09-24  expires: 2020-09-23  usage: S   
[ultimate] (1). alice <alice@example.com>
```

2- Adding encrypt subkey
```
>addkey
```
Select 6 for encrypt only. 
```
You need a passphrase to unlock the secret key for
user: "alice <alice@example.com>"
1024-bit RSA key, ID 0xDCC9E9EDBA6103EB, created 2018-09-24

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 6
```
Then select 'key length', 'expire date', as we did with master key. 
You should end up with one one subkey (usage E) under the existing sign subkey (usage S) and master key (usage C) similar to:
```
pub  1024R/0xDCC9E9EDBA6103EB  created: 2018-09-24  expires: 2020-09-23  usage: C   
                               trust: ultimate      validity: ultimate
sub  1024R/0x690C42EEC8BD1C9B  created: 2018-09-24  expires: 2020-09-23  usage: S   
sub  1024R/0x1E989F8E60C29111  created: 2018-09-24  expires: 2020-09-23  usage: E   
[ultimate] (1). alice <alice@example.com>
```
3- Adding authenticate subkey
```
>addkey
```
Select 8 then untoggle sing and encrypt to leave only certify. 
```
You need a passphrase to unlock the secret key for
user: "alice <alice@example.com>"
1024-bit RSA key, ID 0xDCC9E9EDBA6103EB, created 2018-09-24

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 8
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? S

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? E
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? A
                 
Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Authenticate 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
```
Then select 'key length', 'expire date', as we did with master key. 
You should end up with one master key and three sub keys similar to:
```
pub  1024R/0xDCC9E9EDBA6103EB  created: 2018-09-24  expires: 2020-09-23  usage: C   
                               trust: ultimate      validity: ultimate
sub  1024R/0x690C42EEC8BD1C9B  created: 2018-09-24  expires: 2020-09-23  usage: S   
sub  1024R/0x1E989F8E60C29111  created: 2018-09-24  expires: 2020-09-23  usage: E   
sub  1024R/0xDDE04A7B83EAB9F7  created: 2018-09-24  expires: 2020-09-23  usage: A   
[ultimate] (1). alice <alice@example.com>

```
What we have now is a bandle of public and private keys, all linkes to the master key. 
```
#gpg --homedir ./gnupg-test2 --expert --list-keys
./gnupg-test2/pubring.gpg
-------------------------
pub   1024R/0xDCC9E9EDBA6103EB 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = 9294 033D 3FAD 5CAC 9EA0  00B2 DCC9 E9ED BA61 03EB
uid                            alice <alice@example.com>
sub   1024R/0x690C42EEC8BD1C9B 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = A295 609F 5690 71E4 DBF3  DF6F 690C 42EE C8BD 1C9B
sub   1024R/0x1E989F8E60C29111 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = F16F 5B99 993D 1205 141E  D707 1E98 9F8E 60C2 9111
sub   1024R/0xDDE04A7B83EAB9F7 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = 391F 483E 5290 364C 90D2  A670 DDE0 4A7B 83EA B9F7

# gpg --homedir ./gnupg-test2 --expert --list-secret-keys
./gnupg-test2/secring.gpg
-------------------------
sec   1024R/0xDCC9E9EDBA6103EB 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = 9294 033D 3FAD 5CAC 9EA0  00B2 DCC9 E9ED BA61 03EB
uid                            alice <alice@example.com>
ssb   1024R/0x690C42EEC8BD1C9B 2018-09-24
      Key fingerprint = A295 609F 5690 71E4 DBF3  DF6F 690C 42EE C8BD 1C9B
ssb   1024R/0x1E989F8E60C29111 2018-09-24
      Key fingerprint = F16F 5B99 993D 1205 141E  D707 1E98 9F8E 60C2 9111
ssb   1024R/0xDDE04A7B83EAB9F7 2018-09-24
      Key fingerprint = 391F 483E 5290 364C 90D2  A670 DDE0 4A7B 83EA B9F7
```
## Backup revocation certificates.
We will first ex

## Backup the master private key.
We will do the following:
1- Exporting all secret keys (secret master and subkeys). Save this one away!!!!
```
gpg --homedir ./gnupg-test2 --export-secret-keys --armor --output master-and-subkeys-secret-keys.gpg 0xDCC9E9EDBA6103EB
```
2- Exporting only the secret subkeys 
```
gpg --homedir ./gnupg-test2 --export-secret-subkeys --armor --output subkeys-secret-keys.gpg 0xDCC9E9EDBA6103EB
```
3- Deleting all secret keys from gpg. 
```
gpg --homedir ./gnupg-test2 --delete-secret-keys 0xDCC9E9EDBA6103EB
# then if you list secret keys, nothing exist. Good
gpg --homedir ./gnupg-test2 --list-secret-keys 0xDCC9E9EDBA6103EB
```
4- Importing only the secret sub-kys in step 2. 
```
gpg --homedir ./gnupg-test2 --import subkeys-secret-keys.gpg 
# now if you list secret keys again, you should find only subkeys
gpg --homedir ./gnupg-test2 --list-secret-keys 0xDCC9E9EDBA6103EB
sec#  1024R/0xDCC9E9EDBA6103EB 2018-09-24 [expires: 2020-09-23]
      Key fingerprint = 9294 033D 3FAD 5CAC 9EA0  00B2 DCC9 E9ED BA61 03EB
uid                            alice <alice@example.com>
ssb   1024R/0x690C42EEC8BD1C9B 2018-09-24
      Key fingerprint = A295 609F 5690 71E4 DBF3  DF6F 690C 42EE C8BD 1C9B
ssb   1024R/0x1E989F8E60C29111 2018-09-24
      Key fingerprint = F16F 5B99 993D 1205 141E  D707 1E98 9F8E 60C2 9111
ssb   1024R/0xDDE04A7B83EAB9F7 2018-09-24
      Key fingerprint = 391F 483E 5290 364C 90D2  A670 DDE0 4A7B 83EA B9F7
```
Notice 'sec#' means no master secret key exist. 
