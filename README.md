# OpenVPN for Docker

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/hephaex/openvpn-docker/master/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/martin/openvpn.svg)](https://hub.docker.com/r/hephaex/openvpn-docker/)
[![Docker Stars](https://img.shields.io/docker/stars/martin/openvpn.svg)](https://hub.docker.com/r/hephaex/openvpn-docker/)

As tiny docker image, based on Alpine linux. 

## Quick Start

1. Create the `$OPENVPN_DATA` volume container

        export OPENVPN_DATA=openvpn_data
        docker volume create --name $OPENVPN_DATA

2. Initialize the `$OPENVPN_DATA` container that will hold the configuration files and certificates

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn initopenvpn -u udp://VPN.SERVERNAME.COM
	docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn initpki

3. Start OpenVPN server process

        docker run --name openvpn -v $OPENVPN_DATA:/etc/openvpn -v /etc/localtime:/etc/localtime:ro -d -p 1194:1194/udp --cap-add=NET_ADMIN hephaex/openvpn

4. Generate a client certificate

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn easyrsa build-client-full CLIENTNAME

    - Or without a passphrase (only do this for testing purposes)

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn easyrsa build-client-full CLIENTNAME nopass

5. Retrieve the client configuration with embedded certificates

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn getclient CLIENTNAME > CLIENTNAME.ovpn

    - Or retrieve the client configuration with mssfix set to a lower value (yay Ziggo WifiSpots)

            docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn getclient -M 1312 CLIENTNAME > CLIENTNAME.ovpn

6. Revoke a client certificate

    If you need to remove access for a client then you can revoke the client certificate by running

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn revokeclient CLIENTNAME

7. List all generated certificate names (includes the server certificate name)

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm hephaex/openvpn listcerts

8. Renew the CRL

        docker run -v $OPENVPN_DATA:/etc/openvpn --rm -it hephaex/openvpn renewcrl

* To enable (bash) debug output set an environment variable with the name DEBUG and value of 1 (using "docker -e")
        for example `docker run -e DEBUG=1 --name openvpn -v $OPENVPN_DATA:/etc/openvpn -v /etc/localtime:/etc/localtime:ro -d -p 1194:1194/udp --cap-add=NET_ADMIN hephaex/openvpn`

* To view the log output run `docker logs openvpn`, to view it realtime run `docker logs -f openvpn`

## Settings and features
* OpenVPN 2.4.3
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
* Floating client ip's enabled
* Tweaks for Windows clients
* `net30` topology because it works on the widest range of OS's. `p2p`, for instance, does not work on Windows.
* Google DNS (8.8.4.4 and 8.8.8.8)

* The configuration is located in `/etc/openvpn`
* Certificates are generated in `/etc/openvpn/pki`.


## Tested On

* Clients
  * Android, OpenVPN Connect 1.1.14 (built 56)
  * Android, OpenVPN for Android 0.6.50
  * Windows 10 64 bit using openvpn-2.4.0


Based on [kylemanna/docker-openvpn](https://github.com/kylemanna/docker-openvpn).