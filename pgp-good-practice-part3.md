# PGP part3
## Change existing key
```
gpg2 --edit-key <UID/fingerprint>
gpg>help
```
### Change passphrase
```
gpg2 --edit-key <UID/fingerprint>
gpg>passwd
.
.
gpg> quit
Save changes? (y/N) y
```
Prompt will ask for current and new passphrase, simple. 

### Change expire date 
```
gpg2 --edit-key <UID/fingerprint>
gpg> expire 
Changing expiration time for the primary key.
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
.
.
gpg> quit
Save changes? (y/N) y

```
You can set for example `32m` to make expire date after 32 months.
