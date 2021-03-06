# PGP-introduction
An introduction of PGP and its implementation GPG base on youtube video by Justin Steven at [here](https://www.youtube.com/watch?v=Qp9ZOgLN6V0).

# This tutorial divided into four parts:
- [Part1](./pgp-good-practice-part2.md)
- [Part2](./pgp-good-practice-part2.md)
- [Part3](./pgp-good-practice-part3.md)
- [Utilities](pass-and-gpg.md)
## PGP and GPG
- PGP is the protocol and GPG is the implementation. 
- There are GPG1 and GPG2 implementation in linux. 
- Gpg4Win for windows, kelopatra UI with cmd. 
- Mail clients have some plugin pgp to encrypt your mail.

## Entropy
- When you generate a key, gpg needs random data to base it on. 
- /dev/random - a blocking source of random data. Fed by random occurance e.g keyboard typing pattern (e.g. delay between key press).
- /dev/urandom - a non-blocking source of random data. 
- cat /dev/urandom | xxd

## PGP
- Uses asymmetric crypto 
- provide confidientiality (encryption)
- provide authentication, and Integrity (signing).  
- Give us a web of trust (WoT)- verify party pub key fingerprint by other means then sign it to confirm you already verfified it. 
- It has key servers.


## Usage
### Generate key.
- Good pratcie to create key offline using live cd on physical machine. 
```
gpg --full-gen-key
``` 
- it will ask about key algorithm, 
- key expries (set an expire date, don't warry you can update and extend the expires)
- details, and comment
- user-id
- Then will ask you to provide passphrase to protect your private key (passphrase is used to enc your private key itself, so if it got stolen it is enc and needs passphrase to be decrypted). 

### To list the public keys you have. 
```
gpg --list-key
```
You get a master key (pub), and user id (uid) and sub key (sub).Sub keys may have different capabilities E for encryption, S for signing, and A for authentication, and master key C for certify. 

### To list your private key

```
gpg --list-secret-key
```
Each pair of public and private key should have same fingerprint. 

To refere to one user-id you can use his/her name, email, or last 4bytes of pub key fingerprint. Gpg will use these values to search for the right key.

### Share public key
```
gpg --armor --export <fingerprint> > alice.key
```
Use armor to encode it into base64, so it can be sent as a text file and send it to the other party. 

### import the key
Other party will need to get the public key and import it. 
```
gpg --import alice.key
``` 
Both parties should import each other public key. Then if they list the public key they should get two public keys


### Encrypt using public keys
To encrypt a message use
```
gpg --armor --encrypt message-to-alice.txt
```
It will ask to enter the name of the recipient (name, email, or fingerprint). 
 
It also will show warnning message, as the key is not signed by any trusted party. 

If you try to decrypt the massage you can't, because you don't have the receiver private key. So to be able to decrypt it add your user-id as a reciver as well. 

Send the enc massage to the other party, and he/she can decrypt it as:
```
gpg --decrypt message-to-alice.txt.asc
```
Gpg will search for the public key of the sender and use it to decrypt the message. 


### Signing the key
If you import key then verify it by other means (e.g. call and verify the full fingerprint of the key). Then sign it by your private key. So gpg will not complain each time you try to use it. 
To sign bob key use:
```
gpg --sign-key bob@example.com
```
To sign it localy, just for host use, not to be published in keyserver then 
```
gpg --lsign-key <user-id>
```

Then list the signed keys

```
gpg --list-signs
```
Two public keys should be shown, first is your own key singed by you (slef signiture). The second is bob key singed by bob and by you. Note: there is also bob sub key which only singed by bob master key (sub keys can only be singed by their master key).

Bob public key now has fully trust. 

### Modify key signature
Let's assume you don't trust  Bob anymore and you want to lower your trust on him. So modify your trust on him by changing your signature. 

```
gpg --edit-key Bob
```
Press trust, then select the level of trust. Default is fully turst, ultimate trust is only for your own key.

### Sign message
Signature provides integrity (protect agains message tampring), and authenticity (who is the sender). 

To sign message there are diffrent ways:  
1- Clear Sign 
```
gpg --clearsign message.txt
``` 
It will sign the message but will not encrypt it. It will output the the massage in clear and at the end your signature. 

The receiver can verify the signature, if he has the sender public key. 
```
gpg --verify message.txt.asc
```

2- Sign with the message embedded.
It needs to use --armor for base64, otherwise will get binary. 
```
gpg --armor --sign message.txt
```
The difference is that the output is not a signature only rather it include the message as well. 
So receiver can verify and decrypt the same file.

```
gpg --decrypt message.txt.asc
gpg --verify message.txt.asc
```
3- Sign only without embedding the message

```
gpg --armor --detach-sign message.txt
```
The output is the signature only. Hence, the receiver need two files, the signature and the actual message to verify. 
```
gpg --verify  message.txt.asc  messsage.txt
```

In any case if you try to tamper with the message or its signature the verification will fail. 

## Debug gpg2
As we see, `--sign` actually sign but include the massage somehow (I am not able to find a good resource to figure out what happen exactly). While `--sign --encrypt` sign and encrypt the message as expected.  
To debug gpg2 output file use `--list-packets` it will list packets that used to generate the gpg2 file. 

With `--sign` option
```
gpg2 --sign labs02-answer-sheet.txt       
gpg2 --list-packets labs02-answer-sheet.txt.gpg 
# off=0 ctb=a3 tag=8 hlen=1 plen=0 indeterminate
:compressed packet: algo=1
# off=2 ctb=90 tag=4 hlen=2 plen=13
:onepass_sig packet: keyid 6C3899E161A75894
        version 3, sigclass 0x00, digest 8, pubkey 1, last=1
# off=17 ctb=ad tag=11 hlen=3 plen=1182
:literal data packet:
        mode b (62), created 1555371776, name="labs02-answer-sheet.txt",
        raw data: 1153 bytes
# off=1202 ctb=89 tag=2 hlen=3 plen=307
:signature packet: algo 1, keyid 6C3899E161A75894
        version 4, created 1555371776, md5len 0, sigclass 0x00
        digest algo 8, begin of digest 12 8c
        hashed subpkt 33 len 21 (issuer fpr v4 DB1823524068F6A4FE24CA3A6C3899E161A75894)
        hashed subpkt 2 len 4 (sig created 2019-04-15)
        subpkt 16 len 8 (issuer key ID 6C3899E161A75894)
        data: [2044 bits]
```

With `--sign --encrypt`
```
gpg2 --sign --encrypt labs02-answer-sheet.txt
gpg2 --list-packets labs02-answer-sheet.txt.gpg
gpg: encrypted with 2048-bit RSA key, ID 50A5ADB5BAB87DD2, created 2019-04-11
      "Mohammed <mohammed@ecs.vuw.ac.nz>"
# off=0 ctb=85 tag=1 hlen=3 plen=268
:pubkey enc packet: version 3, algo 1, keyid 50A5ADB5BAB87DD2
        data: [2047 bits]
# off=271 ctb=d2 tag=18 hlen=2 plen=0 partial new-ctb
:encrypted data packet:
        length: unknown
        mdc_method: 2
# off=292 ctb=a3 tag=8 hlen=1 plen=0 indeterminate
:compressed packet: algo=2
# off=294 ctb=90 tag=4 hlen=2 plen=13
:onepass_sig packet: keyid 6C3899E161A75894
        version 3, sigclass 0x00, digest 10, pubkey 1, last=1
# off=309 ctb=ad tag=11 hlen=3 plen=1182
:literal data packet:
        mode b (62), created 1555371839, name="labs02-answer-sheet.txt",
        raw data: 1153 bytes
# off=1494 ctb=89 tag=2 hlen=3 plen=307
:signature packet: algo 1, keyid 6C3899E161A75894
        version 4, created 1555371839, md5len 0, sigclass 0x00
        digest algo 10, begin of digest b3 fc
        hashed subpkt 33 len 21 (issuer fpr v4 DB1823524068F6A4FE24CA3A6C3899E161A75894)
        hashed subpkt 2 len 4 (sig created 2019-04-15)
        subpkt 16 len 8 (issuer key ID 6C3899E161A75894)
        data: [2045 bits]
```
## gpg2 default outpt file extention
By default gpg2 use   
- `.sig` for detatch signature
- `.gpg` for everything else. 
- `.asc` if `--armor` option used regardless of its default extention (overwrite `.sig` and `.gpg`). 

# Resources
1- https://alexcabal.com/creating-the-perfect-gpg-keypair/
2- https://spin.atomicobject.com/2013/11/24/secure-gpg-keys-guide/
3- https://blog.tinned-software.net/create-gnupg-key-with-sub-keys-to-sign-encrypt-authenticate/

