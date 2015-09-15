# A simple single-user [SoftEther VPN][1] server Docker image #

## Setup ##
 - L2TP/IPSec PSK + OpenVPN (Beta)
 - SecureNAT enabled
 - Perfect Forward Secrecy (DHE-RSA-AES256-SHA)
 - make'd from [the official SoftEther VPN GitHub repo][2] master (Note: they don't have any other branches or tags.)

`docker run -d -p 500:500/udp -p 4500:4500/udp -p 1701:1701/tcp ianblenke/softethervpn`

Connectivity tested on Android + iOS devices. It seems Android devices do not require L2TP server to have port 1701/tcp open.

## Credentials ##

All optional:

- `-e PSK`: Pre-Shared Key (PSK), if not set: "notasecret" (without quotes) by default.
- `-e USERNAME`: if not set a random username ("user[nnnn]") is created.
- `-e PASSWORD`: if not set a random weak password is created.

It only creates a single user account with the above credentials in DEFAULT hub.
See the docker log for username and password (unless `-e PASSWORD` is set), which *would look like*:

    ========================
    user6301
    2329.2890.3101.2451.9875
    ========================
Dots (.) are part of the password. Password will not be logged if specified via `-e PASSWORD`; use `docker inspect` in case you need to see it.

Hub & server are locked down; they are given stronger random passwords which are not logged or displayed.

## OpenVPN (Beta) ##

Docker image tag `openvpn` is available for testing, which has OpenVPN compatibilities enabled in addition to IPSec. It will eventually be merged to `latest`.

`docker run -d -p 1194:1194/udp ianblenke/softethervpn:openvpn`

The entire log can be saved and used as an `.ovpn` config file (change as needed).

**Current limitation**: Server CA certificate will be created automatically at runtime but there is no way to retrieve its private key. For now you can create _a self-signed 1024-bit RSA certificate/key pair_ locally and feed both entire contents via `-e CERT` and `-e KEY`. X.509 markers (like `-----BEGIN CERTIFICATE-----`) and any non-BASE64 character (incl. newline) can be omitted and will be ignored.

Examples (assuming bash; note the double-quotes `"` and backticks `` ` ``):

* ``-e CERT="`cat server.crt`" -e KEY="`cat server.key`"``
* `-e CERT="MIIDp..b9xA=" -e KEY="MIIEv..x/A=="`
* `--env-file <cert-and-key.list>`

Certificate volumes support (like `-v` or `--volumes-from`) will be added at some point...

## License ##

[MIT License][3].

  [1]: https://www.softether.org/
  [2]: https://github.com/SoftEtherVPN/SoftEtherVPN
  [3]: https://opensource.org/licenses/MIT
