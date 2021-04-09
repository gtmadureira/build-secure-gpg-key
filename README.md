# Build a secure GPG Key

[web page mode](https://gtmadureira.github.io/build-secure-gpg-key/)<br/><br/>
Start build new keypair...

For this purpose, I recommend you to boot on [tails](https://tails.boum.org) and stay offline, tails linux contain many good things like [onion share](https://onionshare.org/), a persistant encrypted volume, and many others.

## Generate new Key (with only C|capability flag)

    $ gpg --expert --full-generate-key

You can choose to create a RSA, ECC keys, procedure is the same, for this wiki, I create an ECC key:

```txt
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
   (9) ECC and ECC
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (13) Existing key
  (14) Existing key from card
Your selection? 11
```

Next we remove Sign and Authenticate capability, keep only Certify:

```txt
Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate 
Current allowed actions: Sign Certify 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? S

Possible actions for a ECDSA/EdDSA key: Sign Certify Authenticate 
Current allowed actions: Certify 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
```
Next we select a strong and safe curve for elliptic-curve. I chose the `Curve 25519`:

```txt
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
Key expires at Sat 08 Apr 2023 01:29:17 PM -03
Is this correct? (y/N) y
```

And choose bellow an email address active with your name:

```txt
GnuPG needs to construct a user ID to identify your key.

Real name: Elliot Alderson
Email address: elliot_alderson@protonmail.com
Comment: 
You selected this USER-ID:
    "Elliot Alderson <elliot_alderson@protonmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key

    *** Enter passphrase: <Elliot Alderson's long passphrase> ***
    
    *** Repeat passphrase: <Elliot Alderson's long passphrase> ***
    
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 5E90D54315BAD590 marked as ultimately trusted
gpg: revocation certificate stored as '/home/gustavo/.gnupg/openpgp-revocs.d/776B6C5DC848C224DF05E6505E90D54315BAD590.rev'
public and secret key created and signed.

pub   ed25519 2021-04-08 [C] [expires: 2023-04-08]
      776B6C5DC848C224DF05E6505E90D54315BAD590
uid                      Elliot Alderson <elliot_alderson@protonmail.com>
```
If need help to create a secure password, try the [diceware](https://theintercept.com/2015/03/26/passphrases-can-memorize-attackers-cant-guess/) method.

## Change ciphers

Use a strong cipher preferences:

    $ gpg --edit-key elliot_alderson@protonmail.com

```txt
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: tofu+pgp
gpg: depth: 0  valid:   3  signed:   9  trust: 0-, 0q, 0n, 0m, 0f, 3u
gpg: depth: 1  valid:   9  signed:   0  trust: 0-, 0q, 0n, 0m, 9f, 0u
gpg: next trustdb check due at 2022-01-19
sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> setpref SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP
Set preference list to:
     Cipher: AES256, AES192, AES, CAST5, 3DES
     Digest: SHA512, SHA384, SHA256, SHA224, SHA1
     Compression: ZLIB, BZIP2, ZIP, Uncompressed
     Features: MDC, Keyserver no-modify
Really update the preferences? (y/N) y

  *** You need a passphrase to unlock the secret key for ***
  
  *** User: "Elliot Alderson <elliot_alderson@protonmail.com>" ***

  *** Enter passphrase: <Elliot Alderson's long passphrase> ***

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> save
```

## Add Subkeys (S|E|A)

Now, we will create subkey for signing (S), encrypt (E) and authenticate (A):

    $ gpg --expert --edit-key elliot_alderson@protonmail.com

```txt
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Sign 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 6m
Key expires at Tue 05 Oct 2021 01:51:46 PM -03
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>
```

```txt
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 12
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 6m
Key expires at Tue 05 Oct 2021 01:58:10 PM -03
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>
```

```txt
gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only)
  (13) Existing key
  (14) Existing key from card
Your selection? 11

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Sign 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? S

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? A

Possible actions for a ECDSA/EdDSA key: Sign Authenticate 
Current allowed actions: Authenticate 

   (S) Toggle the sign capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? Q
Please select which elliptic curve you want:
   (1) Curve 25519
   (3) NIST P-256
   (4) NIST P-384
   (5) NIST P-521
   (6) Brainpool P-256
   (7) Brainpool P-384
   (8) Brainpool P-512
   (9) secp256k1
Your selection? 1
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 6m
Key expires at Tue 05 Oct 2021 02:01:12 PM -03
Is this correct? (y/N) y
Really create? (y/N) y
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  expires: 2021-10-05  usage: A   
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>
```

In resume, you have one key for each action:

```txt
sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  expires: 2021-10-05  usage: A   
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>
```

Save our work:

```txt
gpg> save
```

## Revocation Cert

Your key has been create, we will create a revocation certificate in case where key is compromised:

    $ gpg --generate-revocation elliot_alderson@protonmail.com > revocation.cert

```txt
sec  ed25519/5E90D54315BAD590 2021-04-08 Elliot Alderson <elliot_alderson@protonmail.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 0
Enter an optional description; end it with an empty line:
> 
Reason for revocation: No reason specified
(No description given)
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```
## Backups into Tar archive

Backup your fresh key:

    $ gpg --armor --export-secret-keys elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_secret.key
    $ gpg --armor --export elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_public.key

Make a tar file:

    $ tar -cf elliot_alderson@protonmail.com_master_keys.tar elliot*.key revocation.cert

Delete useless file:

    $ shred -u elliot*.key revocation.cert

Export all subkeys too temporary, we reimport soon:

    $ gpg --export-secret-subkeys elliot_alderson@protonmail.com > subkeys

Now, we have all necessary files, delete original key from actual system:

    $ gpg --delete-secret-keys elliot_alderson@protonmail.com
    $ gpg --delete-keys elliot_alderson@protonmail.com

Press *Delete key* for each subkeys.

Control than output of `gpg -K` and `gpg -k` do not containt our keys:

    $ gpg -K
    $ gpg -k

Now, we will create a lesser keys with less privilege by reimport our `subkeys` file:

    $ gpg --import subkeys

```txt
gpg: key 5E90D54315BAD590: public key "Elliot Alderson <elliot_alderson@protonmail.com>" imported
gpg: To migrate 'secring.gpg', with each smartcard, run: gpg --card-status
gpg: key 5E90D54315BAD590: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1
```

Clean the `subkeys` file:

    $ shred -u subkeys

Verify than the master signing key is missing (contain `sec#`):

    $ gpg -K

```txt
sec#  ed25519 2021-04-08 [C] [expires: 2023-04-08]
      776B6C5DC848C224DF05E6505E90D54315BAD590
uid           [marginal] Elliot Alderson <elliot_alderson@protonmail.com>
ssb   ed25519 2021-04-08 [S] [expires: 2021-10-05]
ssb   cv25519 2021-04-08 [E] [expires: 2021-10-05]
ssb   ed25519 2021-04-08 [A] [expires: 2021-10-05]
```

The first line with 'sec#' means that the secret key is not usable (you cannot create revocation certificate, change password with this key and other things).

Export this lesser key which will be used on all other devices:

    $ gpg --armor --export-secret-keys elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_secret_lesser.key
    $ gpg --armor --export elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_public_lesser.key

You can make an archive too:

    $ tar -cf elliot_alderson@protonmail.com_lesser_keys.tar elliot*.key

## Import lesser Keys

If you are on tails linux, you can send this archive with [onion share](https://onionshare.org/), it's will generate an url with `.onion`. The service will automatically stop when archive is downloaded. To do this on tails:

Go to Places -> Home, right click on `elliot_alderson@protonmail.com_lesser_keys.tar` and select `Share with OnionShare`.  
So, OnionShare is open, click on `Start sharing`, it generate an url and rename archive.

To import lesser key on other devices:

    $ tar -xvf elliot_alderson@protonmail.com_lesser_keys.tar
    $ gpg --armor --import elliot_alderson@protonmail.com_secret_lesser.key
    $ gpg --armor --import elliot_alderson@protonmail.com_public_lesser.key
    $ shred -u elliot*.key

Trust ultimate on your keys:

    $ gpg --edit-key elliot_alderson@protonmail.com

```txt
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: unknown       validity: marginal
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  expires: 2021-10-05  usage: A   
[marginal] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> trust
pub  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: unknown       validity: marginal
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  expires: 2021-10-05  usage: A   
[marginal] (1). Elliot Alderson <elliot_alderson@protonmail.com>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y

pub  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: marginal
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  expires: 2021-10-05  usage: S   
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  expires: 2021-10-05  usage: E   
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  expires: 2021-10-05  usage: A   
[marginal] (1). Elliot Alderson <elliot_alderson@protonmail.com>
Please note that the shown key validity is not necessarily correct
unless you restart the program.

gpg> save
```

# Set our Primary Key

To use our key, we have to edit `gpg.conf`:

    $ gpg --list-secret-keys --with-subkey-fingerprints

Note fingerprint of our subkey with [S] and [E], we need for configure gpg.conf, in this example:

```txt
sec#  ed25519 2021-04-08 [C] [expires: 2023-04-08]
      776B6C5DC848C224DF05E6505E90D54315BAD590
uid           [ultimate] Elliot Alderson <elliot_alderson@protonmail.com>
ssb   ed25519 2021-04-08 [S] [expires: 2021-10-05]
      5D02A1ECEDD28B9FB2927DE2DCD6B1841F15BC99
ssb   cv25519 2021-04-08 [E] [expires: 2021-10-05]
      35ACF69B4820F094EC2295E598378D667B77DC3D
ssb   ed25519 2021-04-08 [A] [expires: 2021-10-05]
      142C216B40F4B31B09AA9EAEE202997C77B7C45E
```
    $ vim ~/.gnupg/gpg.conf

```txt
# flag S
default-key 5D02A1ECEDD28B9FB2927DE2DCD6B1841F15BC99
# flag E
default-recipient 35ACF69B4820F094EC2295E598378D667B77DC3D
```

If you need share key with friend or post on the web, generate a file like this:

    $ gpg --armor --output key.txt --export elliot_alderson@protonmail.com

## Send Keys to `https://keys.openpgp.org` server

You have to search your key ID, this is a last 8 fingerprint character, `15BAD590` here:

    $ gpg --list-keys --with-fingerprint
    
```txt
pub   ed25519 2021-04-08 [C] [expires: 2023-04-08]
      776B 6C5D C848 C224 DF05  E650 5E90 D543 15BA D590
uid           [ultimate] Elliot Alderson <elliot_alderson@protonmail.com>
sub   ed25519 2021-04-08 [S] [expires: 2021-10-05]
sub   cv25519 2021-04-08 [E] [expires: 2021-10-05]
sub   ed25519 2021-04-08 [A] [expires: 2021-10-05]
```
Keys can be uploaded with GnuPG's `--send-keys` command, but identity information can't be verified that way to make the key searchable by email address ([what does this mean?](https://keys.openpgp.org/about)).

VERY IMPORTANT! >>> After sending your public keys to the server, with command line below, you will receive an email at `elliot_alderson@protonmail.com`, where you need to do the process of verifying the publication(input) of the public keys in the server. If you don't, your public keys will not enter the server!:

    $ gpg --keyserver https://keys.openpgp.org --send-keys 15BAD590

When people search your key, they type that:

    $ gpg --keyserver hkps://keys.openpgp.org --search-keys elliot_alderson@protonmail.com
    
```txt
gpg: data source: https://keys.openpgp.org:443
(1)	Elliot Alderson <elliot_alderson@protonmail.com>
	  263 bit EDDSA key 5E90D54315BAD590, created: 2021-04-08
Keys 1-1 of 1 for "elliot_alderson@protonmail.com".  Enter number(s), N)ext, or Q)uit > 1
gpg: key 5E90D54315BAD590: "Elliot Alderson <elliot_alderson@protonmail.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
```

If the list contains many key, you have to compare the fingerprint.


## When you are compromised

If we have steal your device, we must send a certificate because of course, we are the only ones to have the secret key.

So, you have to boot on tails:

    $ tar -xvf elliot_alderson@protonmail.com_master_keys.tar
    $ gpg --import elliot*.key
    $ gpg --edit-key elliot_alderson@protonmail.com

```txt
gpg> list
gpg> key 1
gpg> key 2
gpg> key 3
gpg> revkey

Do you really want to revoke the selected subkeys? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
Your decision? 1
Enter an optional description; end it with an empty line:
>
Reason for revocation: Key has been compromised
(No description given)
Is this okay? (y/N) y

sec  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: unknown       validity: marginal
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  revoked: 2021-04-09  usage: S   
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  revoked: 2021-04-09  usage: E   
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  revoked: 2021-04-09  usage: A   
[marginal] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> save
```

Export certificate for our other device:

    $ gpg --armor --export > revoked_keys.asc

And on other devices which use your key:

    $ gpg --import revoked_keys.asc
    $ gpg --edit-key elliot_alderson@protonmail.com

```txt
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret subkeys are available.

pub  ed25519/5E90D54315BAD590
     created: 2021-04-08  expires: 2023-04-08  usage: C   
     trust: ultimate      validity: ultimate
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  ed25519/DCD6B1841F15BC99
     created: 2021-04-08  revoked: 2021-04-09  usage: S   
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  cv25519/98378D667B77DC3D
     created: 2021-04-08  revoked: 2021-04-09  usage: E   
The following key was revoked on 2021-04-09 by ? key 5E90D54315BAD590 Elliot Alderson <elliot_alderson@protonmail.com>
ssb  ed25519/E202997C77B7C45E
     created: 2021-04-08  revoked: 2021-04-09  usage: A   
[ultimate] (1). Elliot Alderson <elliot_alderson@protonmail.com>

gpg> quit
```

You have to send each subkeys to `https://keys.openpgp.org`:

    $ gpg --keyserver https://keys.openpgp.org --send-keys DCD6B1841F15BC99
    $ gpg --keyserver https://keys.openpgp.org --send-keys 98378D667B77DC3D
    $ gpg --keyserver https://keys.openpgp.org --send-keys E202997C77B7C45E
    $ rm revoked_keys.asc

That's it.

## When Subkeys expire

When key expire, boot on `tails`, import the real secret key and enhance time of 6 months again,
The procedure is a bit repetitive... :

    $ tar -xvf elliot_alderson@protonmail.com_master_keys.tar 
    $ gpg --armor --import elliot_alderson@protonmail.com_secret.key
    $ gpg --armor --import elliot_alderson@protonmail.com_public.key
    $ gpg --edit-key elliot_alderson@protonmail.com
    
```txt
gpg> key 1
gpg> key 2
gpg> key 3
gpg> expire
gpg> 6m
gpg> save
```

And re-do the same thing than before...(yaaa):

    $ gpg --armor --export-secret-keys --armor elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_secret.key
    $ gpg --armor --export --armor elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_public.key
    $ gpg --generate-revocation elliot_alderson@protonmail.com > revocation.cert
    $ tar -cf elliot_alderson@protonmail.com_master_keys.tar elliot*.key revocation.cert
    $ shred -u elliot*.key revocation.cert
    $ gpg --armor --export-secret-subkeys elliot_alderson@protonmail.com > subkeys
    $ gpg --delete-secret-keys elliot_alderson@protonmail.com
    $ gpg --import subkeys
    $ shred -u subkeys
    $ gpg --armor --export-secret-keys elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_secret_lesser.key
    $ gpg --armor --export elliot_alderson@protonmail.com > elliot_alderson@protonmail.com_public_lesser.key
    $ tar -cf elliot_alderson@protonmail.com_lesser_keys.tar elliot*.key
    $ shred -u elliot*.key

On your laptop, you have to remove older key and reimport the new:

    $ gpg --delete-keys elliot_alderson@protonmail.com
    $ tar -xvf elliot_alderson@protonmail.com_lesser_keys.tar
    $ gpg --armor --import elliot_alderson@protonmail.com_secret_lesser.key
    $ gpg --armor --import elliot_alderson@protonmail.com_public_lesser.key
    $ shred -u elliot*.key

Trust:
    
    $ gpg --edit-key elliot_alderson@protonmail.com

```txt
> trust
> 5 (I trust ultimately)
> save
```

### Troubleshooting

Post an issue to [github](https://github.com/gtmadureira/build-secure-gpg-key/issues)
