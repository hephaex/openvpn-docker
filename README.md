# OpenVPN for Docker

[![License](https://img.shields.io/badge/license-GPL-blue.svg)](https://raw.githubusercontent.com/hephaex/openvpn-docker/master/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/hephaex/openvpn.svg)](https://hub.docker.com/r/hephaex/openvpn-docker/)
[![Docker Stars](https://img.shields.io/docker/stars/hephaex/openvpn.svg)](https://hub.docker.com/r/hephaex/openvpn-docker/)
[![Build Status](https://travis-ci.org/hephaex/webdav-docker.svg?branch=master)](https://travis-ci.org/hephaex/webdav-docker)
[![Bitcoin donate button](https://img.shields.io/badge/bitcoin-donate-yellow.svg)](https://www.coinbase.com/checkouts/16wBMRsdZkNu6Vk7zQetX27aHLnvwusedz)

As tiny docker image, based on Alpine linux.

* The configuration is located in `/etc/openvpn`
* Certificates are generated in `/etc/openvpn/pki`.

## Settings and features
* OpenVPN 2.4.4
* Easy-RSA v3.0.1
* `tun` mode because it works on the widest range of devices. `tap` mode, for instance, does not work on Android, except if the device is rooted.
* The UDP server uses`192.168.255.0/24` for clients.
* TLS 1.2 minimum
* TLS auth key for HMAC security
* Diffie-Hellman parameters for perfect forward secrecy
* Verification of the server certificate subject
* Extended Key usage check of both client and server certificates
* 2048 bits key size
* Client certificate revocation functionality
* SHA256 signature hash
* AES-256-CBC cipher
* TLS cipher limited to TLS-ECDHE-RSA-WITH-AES-128-GCM-SHA256, TLS-ECDHE-ECDSA-WITH-AES-128-GCM-SHA256, TLS-DHE-RSA-WITH-AES-256-GCM-SHA384 or TLS-DHE-RSA-WITH-AES-256-CBC-SHA256
* Compression enabled and set to adaptive
* Google DNS (8.8.4.4 and 8.8.8.8)

## instalation Sequence

1. Create containcer volume defined `$OPENVPN_DATA`
> $ export OPENVPN_DATA=openvpn_data
>
> $ docker volume create --name $OPENVPN_DATA

2. Initialize container using created volume `$OPENVPN_DATA`
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn initopenvpn -u udp://VPN.SERVERNAME.COM
>
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn initpki

3. Start OpenVPN server
> $ docker run --name openvpn -v $OPENVPN_DATA:/etc/openvpn -v /etc/localtime:/etc/localtime:ro -d -p 1194:1194/udp --cap-add=NET_ADMIN hephaex/openvpn

4. Generate a client certificate
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn easyrsa build-client-full CLIENTNAME

5. Retrieve the client configuration with embedded certificates
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn getclient CLIENTNAME > CLIENTNAME.ovpn

6. Revoke a client certificate
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn revokeclient CLIENTNAME

7. List all generated certificates
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn listcerts

8. Renew the CRL
> $ docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn renewcrl


9. To view the log output
> $ docker logs openvpn


## Reference
This repository Based on
  * [jpetazzo/dockvpn](https://github.com/jpetazzo/dockvpn)
  * [kylemanna/docker-openvpn](https://github.com/kylemanna/docker-openvpn.git)
  * [chadoe/docker-openvpn](https://github.com/chadoe/docker-openvpn.git)
