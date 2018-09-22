# Simple password manager (pass)
pass is a tool that manage passwords. It uses your pgp key to encrypt your passwords into files. 
It create one txt file for each password and encrypt it using pgp. 

# Usage
## Install pass
```
sudo apt install pass
```
## Set pgp key to use
pass needs to use pgp key to encrypt and decrypt your password files. 
```
pass init your-gpg-id
```
## Generat a password for a new sign up account or whatever.  
```
pass generate <password for> <number of char> 
#e.g.
pass generate facebook 16
```
It will print out random char to be used as password and will save it into encrypted file in ~/.password-store/facebook.gpg 
you can check that by decrypt it directly using gpg. 
```
gpg -d ~/.password-store/facebook.gpg
```

## Add existing password to pass
```
pass insert <password for> 
# e.g. 
pass insert gmail
```
It will ask you to type the password then it will save it. 

## To show what password for are saved.
```
pass
```

## To retrive any password 
```
pass <password for>
#e.g.
pass facebook
```
It will decrypt tha facebook password that is stored in './password-store/facebook.pgp' using your pgp key and print it out.

To copy it into clipboard and not print it into stdout use '-c' option
```
pass -c <password for>
#e.g.
pass -c facebook
```

