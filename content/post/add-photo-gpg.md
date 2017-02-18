+++
title = "Add Photo to Your PGP Key"
date = "2016-02-28"
tags = ["gpg"]
+++

# Strip JPEG file to remove meta data

`jpegtran -copy none -outfile me_no_meta.jpg src.jpg`

# Add Photo

```.shell
gpg --fingerprint
/root/.gnupg/pubring.kbx
------------------------
pub   rsa2048/88EE1A77 2016-02-10
      Key fingerprint = DE53 0326 5015 7A0B 7257  9D72 E3AD 5EAD 88EE 1A77
uid         [ultimate] Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>
sub   rsa2048/EEC7BB16 2016-02-10

gpg --edit-key --expert 88EE1A77

sec  rsa2048/88EE1A77
     created: 2016-02-10  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/EEC7BB16
     created: 2016-02-10  expires: never       usage: E   
[ultimate] (1). Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>

gpg> addphoto

Pick an image to use for your photo ID.  The image must be a JPEG file.
Remember that the image is stored within your public key.  If you use a
very large picture, your key will become very large as well!
Keeping the image close to 240x288 is a good size to use.

Enter JPEG filename for photo ID: me_no_meta.jpg
This JPEG is really large (350117 bytes) !
Are you sure you want to use it? (y/N) y
Is this photo correct (y/N/q)? y

sec  rsa2048/88EE1A77
     created: 2016-02-10  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/EEC7BB16
     created: 2016-02-10  expires: never       usage: E   
[ultimate] (1). Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>
[ unknown] (2)  [jpeg image of size 350117]

gpg> save
```

# View Photo from a PGP Key
- You need to have `xloadimage` installed.

`gpg --list-options show-photos -k 88EE1A77`

# Remove Photo
- select the photo id as working uid
- delete the id and save

```.shell
gpg --edit-key --expert 88EE1A77
sec  rsa2048/88EE1A77
     created: 2016-02-10  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/EEC7BB16
     created: 2016-02-10  expires: never       usage: E   
[ultimate] (1). Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>
[ultimate] (2)  [jpeg image of size 350117]

gpg> uid 2

sec  rsa2048/88EE1A77
     created: 2016-02-10  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/EEC7BB16
     created: 2016-02-10  expires: never       usage: E   
[ultimate] (1). Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>
[ultimate] (2)* [jpeg image of size 350117]

gpg> deluid
Really remove this user ID? (y/N) y

sec  rsa2048/88EE1A77
     created: 2016-02-10  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/EEC7BB16
     created: 2016-02-10  expires: never       usage: E   
[ultimate] (1). Shanthakumar (http://5hanth.github.io) <mail@shanth.tk>

gpg> save
```
