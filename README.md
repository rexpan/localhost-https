1. Root SSL certificate

Generate a RSA-2048 key `rootCA.key`.
This file will be used as the key to generate the Root SSL certificate.

```sh
openssl genrsa -des3 -out rootCA.key 2048
123ABCxyz
```

Create a new Root SSL certificate `namedrootCA.pem`

```
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem
```

2. Trust Root SSL certificate

Mac: Keychain Access > Category > Certificates > File > Import Items > When using this certificate: Always Trust

Windows: Certificate > Trusted Root Certificate > Certificate > Import > rootCA.pem



3. Domain SSL certificate


Create a certificate key for `localhost`, `server.key`, using the configuration settings stored in `server.csr.cnf`.

```
openssl req -new -sha256 -nodes -out server.csr -newkey rsa:2048 -keyout server.key -config <( cat server.csr.cnf )
```

A certificate signing request `server.crt` is issued via the root SSL certificate to create a domain certificate for `localhost`.

```
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 500 -sha256 -extfile v3.ext
```