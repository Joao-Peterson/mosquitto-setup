# mosquitto-setup

- [mosquitto-setup](#mosquitto-setup)
- [Mosquitto conf](#mosquitto-conf)
- [Certificates](#certificates)
- [Usage](#usage)
  - [Mosquitto pub](#mosquitto-pub)
  - [Mosquitto sub](#mosquitto-sub)

# Mosquitto conf

[mosquitto.conf](./mosquitto.conf)

# Certificates

Sources:
- [https://mosquitto.org/man/mosquitto-tls-7.html](https://mosquitto.org/man/mosquitto-tls-7.html)
- [http://www.steves-internet-guide.com/mosquitto-tls/](http://www.steves-internet-guide.com/mosquitto-tls/)
- [https://dzone.com/articles/secure-communication-with-tls-and-the-mosquitto-broker](https://dzone.com/articles/secure-communication-with-tls-and-the-mosquitto-broker)

First create a CA certificate to sign the server certificate, wich will be used as the mosquitto broker certificate, and the CA one as means for the clients to verify the server one wich in turn will enable the tls connection.

Create a key for the CA:
```console
openssl genrsa -out ca.key 2048
```

Create the certificate for the CA:
```console
openssl req -new -x509 -days 385 -key ca.key -out ca.crt
```

Create a key for the server:
```console
openssl genrsa -out server.key 2048
```

Create a certificate signing request for the server: 
```console
openssl req -out server.csr -key server.key -new
```
Remember to when prompted by the Common Name use your server host name, either an ip address or domain:
```console
Country Name (2 letter code) [AU]:BR
State or Province Name (full name) [Some-State]:SC
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:192.168.0.15
Email Address []:
```
Common Name (e.g. server FQDN or YOUR name) []:**192.168.0.15**

Generate the certificate:
```console
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365
```

# Usage

## Mosquitto pub
```console
mosquitto_pub -h 192.168.0.15 -p 8883 -u test_pub -P 1234 -t test/a -m "hello" --cafile ca.crt 
```


## Mosquitto sub

```console
mosquitto_sub -h 192.168.0.15 -p 8883 -u test_client -P 1234 -t test/a --cafile ca.crt
```