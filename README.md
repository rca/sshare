# sshare - Securely share files with RSA keys

This is a script that uses RSA keys to encrypt and decrypt a data file.  Note, this script exists to solve a chicken-and-egg problem; like when you need to transfer secrets over the public Internet and all you have is openssl.  In other words, if you can use PGP, SSH, SFTP, etc. those are much better, use those alternatives instead.


## Note on LibreSSL

This script is not compatible with LibreSSL.  If you have alternative versions of OpenSSL, for example installed with homebrew on MacOS, point to it using the `SSHARE_OPENSSL` environment variable:

```
$ export SSHARE_OPENSSL=/usr/local/opt/openssl@1.1/bin/openssl
```


## Generate a key

To generate a key, run the command `bash sshare genkey`:

```
$ bash sshare genkey
Generating RSA private key, 4096 bit long modulus (2 primes)
........++++
........................................................................................................................................................++++
e is 65537 (0x010001)
writing RSA key

The key /Users/berto/.sshare/sshare.key has been created.

Copy the key below and send to the person who wants to share with you:

-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAsNOT14NI6v91m97jkWXh
5q68Ivgp+U9u01qQU6H6PQtEnq6U1aVaCWvtbxz67INnTAqaid8lUcbPuXsI0PAb
WqZkg7m8mD+SIzb8aZ3MC0huTfv50wBQoCDdINjAO0yzohJdJ5Wa2z2lBNx9/YE2
KfSMjATfUXFsP2ISGBgqUslNVkjqbbGL0xuOalDhCMay0WECm7bmFw8/cfAE64XZ
86TKLuNZL/fD31umXBGqnVScA424Cx3vQ7NGy136900Ao9THivJA8fC3t5M4r1tU
BI68UMj2oryC9VcON6nDsz1mYlnmoVxIMpc2eZO7vmA95z/g1XWcKCh3QJUT9HlY
wvqg7aPg+bO4rGwkKF135qvI+WcN7124Y59mM6E0UYqVM75vx/4wlb3zFPvVF4Nl
u1pbQk2HJIFFT1wfUQ1Y8xVVf8TZQk58FKUtMbslq4o/XxQOMkkfBtB/HCFyRvgY
/hAw96/Io9keSvx5G6+uvbcyfxW8IChXpUsoFxYsUwt9VKs506BYlcH5GLqBOTe6
lWqPy/u00rm38x5vjKzJpcWLikk/3Q2CR2uCtQ3AaZdqB3jjaBVa0yTGY/ExzFNZ
xVZrMAK+/Cw/GZXbKgKi9dQ+tSfdW7MqUDOr+x9nzRGIOL2xR7QAFDUq/RPUX0xI
p+DsfP+gCtJw0ZvT7TqoqGMCAwEAAQ==
-----END PUBLIC KEY-----
```

In the output above send the following text to the sender:

```
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAsNOT14NI6v91m97jkWXh
5q68Ivgp+U9u01qQU6H6PQtEnq6U1aVaCWvtbxz67INnTAqaid8lUcbPuXsI0PAb
WqZkg7m8mD+SIzb8aZ3MC0huTfv50wBQoCDdINjAO0yzohJdJ5Wa2z2lBNx9/YE2
KfSMjATfUXFsP2ISGBgqUslNVkjqbbGL0xuOalDhCMay0WECm7bmFw8/cfAE64XZ
86TKLuNZL/fD31umXBGqnVScA424Cx3vQ7NGy136900Ao9THivJA8fC3t5M4r1tU
BI68UMj2oryC9VcON6nDsz1mYlnmoVxIMpc2eZO7vmA95z/g1XWcKCh3QJUT9HlY
wvqg7aPg+bO4rGwkKF135qvI+WcN7124Y59mM6E0UYqVM75vx/4wlb3zFPvVF4Nl
u1pbQk2HJIFFT1wfUQ1Y8xVVf8TZQk58FKUtMbslq4o/XxQOMkkfBtB/HCFyRvgY
/hAw96/Io9keSvx5G6+uvbcyfxW8IChXpUsoFxYsUwt9VKs506BYlcH5GLqBOTe6
lWqPy/u00rm38x5vjKzJpcWLikk/3Q2CR2uCtQ3AaZdqB3jjaBVa0yTGY/ExzFNZ
xVZrMAK+/Cw/GZXbKgKi9dQ+tSfdW7MqUDOr+x9nzRGIOL2xR7QAFDUq/RPUX0xI
p+DsfP+gCtJw0ZvT7TqoqGMCAwEAAQ==
-----END PUBLIC KEY-----
```


## Encrypt secrets.txt

To encrypt a file you will need the recipient's public key, an example is shown above.  For example, to encrypt the file `secrets.txt` with the recipient's public key located at `/tmp/recipient.pub`, run the command `bash sshare enc /tmp/recipient.pub secrets.txt`:

```
$ bash sshare enc /tmp/recipient.pub secrets.txt
a secret.key.enc
a secrets.txt.enc
Send the recipient the file:
secrets.txt.sshare.tgz
```

The file `secrets.txt.sshare.tgz` is encrypted for sending to the recipient.


## Decrypt secret.txt.sshare.tgz

The file `secrets.txt.sshare.tgz` can be decrypted with the command `bash sshare dec ~/.sshare/sshare.key secrets.txt`:

```
$ bash sshare dec ~/.sshare/sshare.key secrets.txt.sshare.tgz
secret.key.enc
secrets.txt.enc
```

The file `secrets.txt` is now clear text:

```
$ cat secrets.txt
password123
```
