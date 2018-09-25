# Create a key
- Create your key offline, using boot from CD. 
- Set an expire date for your keys, so if you lost them they will not be there forever. 
- Maybe you need to create a replication certificate for keys, so if you lost them 
then you distribute the replication certificate to stop others from using it. 
- Expriation date doesn't means you can't update it. You still able to update it.
- Expration date also force others to update your key, if you add subkeys or extend it. 


# GPG Configuration 
Go to '.gnupg/gpg.conf', it includs comment that explains each option such as:
- You can configure default-key to encrypt with. 
- You can set defalut recipient, and put yourself as well as recipient with the intended recipient. 
This will enable you to decrypt messages that you encrypt for others. 
- Set 'keyid-format' to (short 32bits, long 64bits). Better not to use short, as collision can happen (look at Evil 32).
- Set agent 
- Set keyserver 
- Set digest and cipher preferences is IMPORTANT before you create or sing keys. 

A good gpg practice can be found in [here](https://riseup.net/en/security/message-security/openpgp/best-practices). 
A good example of '.gnugp/gpg.conf' can be found [here](https://raw.githubusercontent.com/ioerror/duraconf/master/configs/gnupg/gpg.conf) ,
and [here](https://gist.github.com/bnagy/8914f712f689cc01c267#gpg-gpgrc).

Here is what can be a good configuration file may look like at the time of writting [gpg.conf](https://github.com/alshaboti/PGP-introduction/blob/master/gpg.conf)

Now you can start creating keys and do the signature. 
# Create revocation certificate 
Revocation certificate is used to tell other parties (e.g. upload to keyserver) that your key is not valid anymore and they should not use it to send to you or accept any message.

It is a good practice to create revocation certificates at the time you create your keys. Then store it in a safe place, in case you come to a time when your master key get compromised or missed or for any other reason. At that time you will need to use these certificates.

To create revocaation certificate
```
gpg --armor --gen-revoke <your-id>
```
You should save it in a secure place, otherwise it maybe used to revoke your key while you still use it. 

# NOTES
- When you use pgp to encrypt message to 10 recipients, does it encrypts it ten times.
Rather it generate random symmetric key to encrypt the message. 
Then encrypts the symmetric key using recipeint public key, so they can decrypt the symmetric key and use it to decrypt the message. 

Each recipient will receive an ecrypted message (by the symmetric key), and many copies of symmetric key encrypted by different recipeient 
public key. One of them is his public key, hence, pgp label them so that each recipient can know which one to decrypt.

However, including the recipeints lable on the encrypted symmetric key leak some information about to whom other than you this message sent to. 
to remove that set 'throw-keyids'

- Note: Asymmetric encryption is more computationaly expensive than symmetric encryption because it uses expoentiation while symmetric uses
transportation and permutation which are cheaper than the exponentiation. 
