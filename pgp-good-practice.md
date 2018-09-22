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

Here is what can be a good configuration file
```
# This is an implementation of the Riseup OpenPGP Best Practices
# don't send key comment, it leaks some info. 
no-greeting

# If you do not pass a recipient to gpg, it will ask for one.  Using
# this option you can encrypt to a default key.  Key validation will
# not be done in this case.  The second form uses the default key as
# default recipient.

#default-recipient some-user-id
default-recipient-self
require-cross-certification

#-----------------------------
# default key
#-----------------------------

# The default key to sign with. If this option is not used, the default key is
# the first key found in the secret keyring

#default-key 0xD8692123C4065DEA5E0F3AB5249B39D24F25E3B6


#-----------------------------
# behavior
#-----------------------------

# Disable inclusion of the version string in ASCII armored output
no-emit-version

# Disable comment string in clear text signatures and ASCII armored messages
no-comments

# Display long key IDs
keyid-format 0xlong

# List all keys (or the specified ones) along with their fingerprints
with-fingerprint

# Display the calculated validity of user IDs during key listings
list-options show-uid-validity
verify-options show-uid-validity

# Try to use the GnuPG-Agent. With this option, GnuPG first tries to connect to
# the agent before it asks for a passphrase.
#use-agent


#-----------------------------
# keyserver
#-----------------------------

# This is the server that --recv-keys, --send-keys, and --search-keys will
# communicate with to receive keys from, send keys to, and search for keys on
#keyserver hkps://hkps.pool.sks-keyservers.net

# Provide a certificate store to override the system default
# Get this from https://sks-keyservers.net/sks-keyservers.netCA.pem
#keyserver-options ca-cert-file=/usr/local/etc/ssl/certs/hkps.pool.sks-keyservers.net.pem

# Set the proxy to use for HTTP and HKP keyservers - default to the standard
# local Tor socks proxy
# It is encouraged to use Tor for improved anonymity. Preferrably use either a
# dedicated SOCKSPort for GnuPG and/or enable IsolateDestPort and
# IsolateDestAddr
#keyserver-options http-proxy=socks5-hostname://127.0.0.1:9050

# Don't leak DNS, see https://trac.torproject.org/projects/tor/ticket/2846
#keyserver-options no-try-dns-srv

# When using --refresh-keys, if the key in question has a preferred keyserver
# URL, then disable use of that preferred keyserver to refresh the key from
#keyserver-options no-honor-keyserver-url

# When searching for a key with --search-keys, include keys that are marked on
# the keyserver as revoked
#keyserver-options include-revoked


#-----------------------------
# algorithm and ciphers
#-----------------------------

# list of personal digest preferences. When multiple digests are supported by
# all recipients, choose the strongest one
personal-cipher-preferences AES256 AES192 AES CAST5

# list of personal digest preferences. When multiple ciphers are supported by
# all recipients, choose the strongest one
personal-digest-preferences SHA512 SHA384 SHA256 SHA224

# message digest algorithm used when signing a key
cert-digest-algo SHA512

# This preference list is used for new keys and becomes the default for
# "setpref" in the edit menu
default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP Uncompressed

throw-keyids
```

Now you can start creating keys and do the signature. 

# FAQ
- When you use pgp to encrypt message to 10 recipients, does it encrypts it ten times.
Rather it generate random symmetric key to encrypt the message. 
Then encrypts the symmetric key using recipeint public key, so they can decrypt the symmetric key and use it to decrypt the message. 

Each recipient will receive an ecrypted message (by the symmetric key), and many copies of symmetric key encrypted by different recipeient 
public key. One of them is his public key, hence, pgp label them so that each recipient can know which one to decrypt.

However, including the recipeints lable on the encrypted symmetric key leak some information about to whom other than you this message sent to. 
to remove that set 'throw-keyids'

- Note: Asymmetric encryption is more computationaly expensive than symmetric encryption because it uses expoentiation while symmetric uses
transportation and permutation which are cheaper than the exponentiation. 
