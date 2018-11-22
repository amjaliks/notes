# Import multiple pairs of certificate and private key into PKCS#12 keystore

Jetty supports SNI. All you need is to add multiple certificates to a keystore.

OpenSSL supports only 1 private key per PKCS#12 store, thus, you need you use `keytool` from Java toolset to create a keystore with multiple private keys. However, `keytool` can't import a private key separatly, thus, before you can use `keytool`, you need to combine a ceritificate (or a full chain) and a private key into PKCS#12 with `openssl`.

##### Step 1

For each pair of a cerificate (or a full chain) and a private key create a temporary keystore. Set an export password, otherwise you will get NPE during the next step! And set an alias (`-name`) for convience during next the second step.

```
openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -name example.com -out example.com.p12
```

Just in case you use Let's Encrypt:

```
openssl pkcs12 -export -in /etc/letsencrypt/live/example.com/fullchain.pem -inkey /etc/letsencrypt/live/example.com/privkey.pem -name example.com -out example.com.p12
```

##### Step 2

Import all separate keystores into the final one. Run for each keystore.

```
keytool -importkeystore -alias getpp.net -srckeystore getpp.net.p12 -deststoretype pkcs12 -destkeystore keystore  
```

Now you can use `keystore` with your Jetty setup.

_2018-11-22_