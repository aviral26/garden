- [Certificates](#certificates)
  - [Generating self-signed certificates](#generating-self-signed-certificates)
  - [Checking certificates](#checking-certificates)
  - [String Manipulation](#string-manipulation)
  - [Remove password from a pkcs12](#remove-password-from-a-pkcs12)

# Certificates

Overview: https://sites.google.com/site/ddmwsst/digital-certificates

## Generating self-signed certificates

```shell
# Use a DNS name in subject alt name
serverName=example.com
openssl req \
  -x509 \
  -sha256 \
  -nodes \
  -newkey rsa:2048 \
  -days 365 \
  -subj "/CN=$serverName/O=example inc/C=IN/ST=Haryana/L=Gurgaon" \
  -addext "subjectAltName=DNS:$serverName" \
  -keyout example.key \
  -out example.crt

# Use an IP address in subject alt name
serverIp=10.0.0.1
openssl req \
  -x509 \
  -sha256 \
  -nodes \
  -newkey rsa:2048 \
  -days 365 \
  -subj "/CN=$serverIp/O=example inc/C=IN/ST=Haryana/L=Gurgaon" \
  -addext "subjectAltName=IP:$serverIp" \
  -keyout example.key \
  -out example.crt
```

## Checking certificates

```shell
# Check a key
openssl rsa -in example.key -check

# Check a certificate
openssl x509 -in example.crt -text -noout

# Check server TLS certificates
openssl s_client -connect example.com:443

# Obtain TLS certificate thumbprint
openssl s_client -connect example.com:443 < /dev/null 2>/dev/null | openssl x509 -fingerprint -noout -in /dev/stdin

# Check expiry
openssl s_client -servername example.com -connect example.com:443 2>/dev/null | openssl x509 -noout -date
```

## String Manipulation

```shell
# Certificate as a single string (no line breaks)
awk 'NF {sub(/\r/, ""); printf "%s\\n",$0;}'  c.pem
```

## Remove password from a pkcs12

```shell
# PASSWORD is your current password
# YourPKCSFile is the file you want to convert
# NewPKCSWithoutPassphraseFile is the target file for the PKCS12 without passphrase

# First, extract the certificate:
openssl pkcs12 -clcerts -nokeys -in "YourPKCSFile" -out certificate.crt -password pass:PASSWORD -passin pass:PASSWORD

# Second, the CA key:
openssl pkcs12 -cacerts -nokeys -in "YourPKCSFile" -out ca-cert.ca -password pass:PASSWORD -passin pass:PASSWORD

# Third, the private key:
openssl pkcs12 -nocerts -in "YourPKCSFile" -out private.key -password pass:PASSWORD -passin pass:PASSWORD -passout pass:TemporaryPassword

# Now remove the passphrase:
openssl rsa -in private.key -out "NewKeyFile.key" -passin pass:TemporaryPassword

# Put things together for the new PKCS file:
cat "NewKeyFile.key" "certificate.crt" "ca-cert.ca" > PEM.pem
openssl pkcs12 -export -nodes -CAfile ca-cert.ca -in PEM.pem -out "NewPKCSWithoutPassphraseFile"
```
