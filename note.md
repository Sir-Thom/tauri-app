# RPM Note 

to see the info of the package
```bash
rpm -qi package_name
```
example output
```bash
rpm -qi tauri-app-0.0.0-1.x86_64.rpm
Name        : tauri-app
Epoch       : 0
Version     : 0.0.0
Release     : 1
Architecture: x86_64
Install Date: (not installed)
Group       : Unspecified
Size        : 11456527
License     : 
Signature   : EdDSA/SHA512, jeu 30 mai 2024 10:32:44, Key ID f1dbd23fecdd57e8
Source RPM  : (none)
Build Date  : mer 22 mai 2024 14:09:10
Build Host  : (none)
Summary     : A Tauri App
Description :
A Tauri App
Distribution: (none)
```

## Add source rpm to the package

There are two type of rpm package the binary package and the source package. SRPM
the SRPM and the RPM work share the file format and tooling.
But the SRPM contains the source code and the spec file used to build the RPM package.
If you want to have a SRPM you will need to build the package by yourself.
because tauri build the rpm package with the binary package only.

## add post,pre install and remove script to the package
1: create a folder `scripts` in src-tauri directory
```bash
mkdir scripts
```
now create the script file in the folder
```bash
touch scripts/postinstall.sh
touch scripts/preinstall.sh
touch scripts/preremove.sh
touch scripts/postremove.sh
```
here a simple example of the script
```bash
echo "-------------"
echo "This is pre"
echo "Install Value: $1"
echo "Upgrade Value: $1"
echo "Uninstall Value: $1"
echo "-------------"
```

2: add the script to the `tauri.conf.json`
```toml
{
  "productName": "tauri-app",
  "version": "0.0.0",
  "identifier": "com.tauri.devthom",
  "build": {
    "beforeDevCommand": "npm run dev",
    "devUrl": "http://localhost:1420",
    "beforeBuildCommand": "npm run build",
    "frontendDist": "../dist"
  },
  "app": {
    "withGlobalTauri": true,
    "windows": [
      {
        "title": "tauri-app",
        "width": 800,
        "height": 600
      }
    ],
    "security": {
      "csp": null
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/128x128@2x.png",
      "icons/icon.icns",
      "icons/icon.ico"
    ],
    "category": "Utility",
    "linux": {
      "rpm": {
        "epoch": 0,
        "files": {},
        "release": "1",
        // add the script here
        "preInstallScript": "/path/to/your/project/src-tauri/scripts/prescript.sh",
        "postInstallScript": "/path/to/your/project/src-tauri/scripts/postscript.sh",
        "preRemoveScript": "/path/to/your/project/src-tauri/scripts/prescript.sh",
        "postRemoveScript": "/path/to/your/project/src-tauri/scripts/postscript.sh"
      }
    }
  }
}
```

## setting conflict,provides,depends,files,obsoletes, and Desktop file  to the package 
conflict :  will be used to prevent the installation of the package if the package as conflict with another package.
for exemple if you update one rpm package that your app depends on and the new version of the package is not compatible with your app.

provides : will be used to tell the list of RPM dependencies your application provides.

Depends: will be used to tell the list of RPM dependencies your application needs to run.

Files: will be used to tell what files to include on the package.

Obsoletes: will be used to tell the list of RPM dependencies your application obsoletes. 
Note: if this package is installed, packages listed as “obsoletes” will be automatically removed (if they are present).

Desktop file: will be used to add custom  desktop file to the package.

to use it add this to your `tauri.conf.json`
```toml
{
  "productName": "tauri-app",
  "version": "0.0.0",
  "identifier": "com.tauri.devthom",
  "build": {
    "beforeDevCommand": "npm run dev",
    "devUrl": "http://localhost:1420",
    "beforeBuildCommand": "npm run build",
    "frontendDist": "../dist"
  },
  "app": {
    "withGlobalTauri": true,
    "windows": [
      {
        "title": "tauri-app",
        "width": 800,
        "height": 600
      }
    ],
    "security": {
      "csp": null
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/128x128@2x.png",
      "icons/icon.icns",
      "icons/icon.ico"
    ],
    "category": "Utility",
    "linux": {
      "rpm": {
        "epoch": 0,
        "files": {},
        "release": "1",
          "preInstallScript": "/path/to/your/project/src-tauri/scripts/prescript.sh",
        "postInstallScript": "/path/to/your/project/src-tauri/scripts/postscript.sh",
        "preRemoveScript": "/path/to/your/project/src-tauri/scripts/prescript.sh",
        "postRemoveScript": "/path/to/your/project/src-tauri/scripts/postscript.sh"
        "conflicts": ["oldLib.rpm"],
        "depends": ["newLib.rpm"],
        "obsoletes": ["veryoldLib.rpm"],
        "provides": ["coolLib.rpm"],
        "desktopTemplate": "/path/to/your/project/src-tauri/desktop-template.desktop"
      }
    }
  }
}
```

## Add a license to the package
1: create a file with the license content
2: add the license file to the package
```toml
package]
name = "tauri-app"
version = "0.0.0"
description = "A Tauri App"
authors = ["you"]
edition = "2021"
"license" = "MIT" # add the license here 

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[build-dependencies]
tauri-build = { version = "2.0.0-beta", features = [] }

[dependencies]
tauri = { version = "2.0.0-beta", features = [] }
tauri-plugin-shell = "2.0.0-beta"
serde = { version = "1", features = ["derive"] }
serde_json = "1"
``` 
3: build the package
```bash
npm run tauri build --release
```
```bash
rpm -qi tauri-app-0.0.0-1.x86_64.rpm
Name        : tauri-app
Epoch       : 0
Version     : 0.0.0
Release     : 1
Architecture: x86_64
Install Date: (not installed)
Group       : Unspecified
Size        : 11407895
License     : MIT # the license is added 
Signature   : EdDSA/SHA512, jeu 30 mai 2024 10:38:23, Key ID f1dbd23fecdd57e8
Source RPM  : (none)
Build Date  : jeu 30 mai 2024 10:38:04
Build Host  : (none)
Summary     : A Tauri App
Description :
A Tauri App
Distribution: (none)
```

## add gpg key to package
1:   first let's generate a key 
```bash 
gpg --gen-key
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg2 --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Tauri-App
Email address: Dev@human.net
You selected this USER-ID:
    "Tauri-App <Dev@human.net>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: revocation certificate stored as '/home/thomas/.gnupg/openpgp-revocs.d/2A0D30287277E2E7186AC426F1DBD23FECDD57E8.rev'
public and secret key created and signed.

pub   ed25519 2024-05-30 [SC] [expires: 2027-05-30]
      2A0D30287277E2E7186AC426F1DBD23FECDD57E8
uid                      Tauri-App <Dev@human.net>
sub   cv25519 2024-05-30 [E] [expires: 2027-05-30]
```
to confirm the key exist 
```bash 
gpg --list-keys
[keyboxd]
---------
pub   ed25519 2024-05-30 [SC] [expires: 2027-05-30]
      2A0D30287277E2E7186AC426F1DBD23FECDD57E8
uid           [ultimate] Tauri-App <Dev@human.net>
sub   cv25519 2024-05-30 [E] [expires: 2027-05-30]

```
2: Export the Public key.

```bash 
gpg --export -a 'Tauri-App' > RPM-GPG-KEY-Tauri-App
cat RPM-GPG-KEY-Tauri-App
-----BEGIN PGP PUBLIC KEY BLOCK-----

mDMEZliGVBYJKwYBBAHaRw8BAQdAirs7KR8vfYvVae+AQiwfSA5EvHXH5/LDx6kE
9q6TTCm0GVRhdXJpLUFwcCA8RGV2QGh1bWFuLm5ldD6ImQQTFgoAQRYhBCoNMChy
d+LnGGrEJvHb0j/s3VfoBQJmWIZUAhsDBQkFo5qABQsJCAcCAiICBhUKCQgLAgQW
AgMBAh4HAheAAAoJEPHb0j/s3VfoYwIBAO+RY4y9WFSjXvaZm41ehzVwABUKTyj4
qNZCnhh+phZcAPsHg5aOeb7H/9wVGz152MFI/uCfmQv8GwV2MVGtWazIDbg4BGZY
hlQSCisGAQQBl1UBBQEBB0CIE9xqTH2HCfP9yZU+ceUzneKOD7vCOPtQISsrny+I
JgMBCAeIfgQYFgoAJhYhBCoNMChyd+LnGGrEJvHb0j/s3VfoBQJmWIZUAhsMBQkF
o5qAAAoJEPHb0j/s3Vfo8GkBAJMXiewzpoM5lVsGej7nP+Bt+nQcdBNm0Zanq8f6
Z+TAAQCPZqBDroEeF0yQ7D6EMEfBcVvM8aPJtDIyuxFQoABoAg==
=lCZ5
-----END PGP PUBLIC KEY BLOCK-----
```
Note: The key is stored in the file RPM-GPG-KEY-Tauri-App and Tauri-app can be changed to the name of the key. 

now let import the key to the rpm database
```bash
sudo rpm --import RPM-GPG-KEY-Tauri-App
# to verify the key is imported
rpm -q gpg-pubkey --qf '%{name}-%{version}-%{release} --> %{summary}\n'
gpg-pubkey-ecdd57e8-66588654 --> Tauri-App <Dev@human.net> public key
```

3: Edit the file ~/.rpmmacros in order to utilize the key.
```bash
cat ~/.rpmmacros
%_signature gpg
%_gpg_path /home/thomas/.gnupg 
%_gpg_name Tauri-App
%_gpgbin /usr/bin/gpg2
```
4 Sign the package with the key
```bash
cd your-project/src-tauri/target/release/bundle/rpm
rpm --addsign tauri-app-0.0.0-1.x86_64.rpm # can also use rpmsign --addsign
```
 enter the password for the key
```
to verify the package is signed 
```bash 
rpm -K tauri-app-0.0.0-1.x86_64.rpm
tauri-app-0.0.0-1.x86_64.rpm: digests signatures OK
```



