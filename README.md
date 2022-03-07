# OpenVPN-client

This is a docker image of an OpenVPN client tied to a SOCKS proxy server.  It is
useful to isolate network changes (so the host is not affected by the modified
routing).

This supports directory style (where the certificates are not bundled together in one `.ovpn` file) and those that contains `update-resolv-conf`

(For the same thing in WireGuard, see [robinaa/docker-wireguard-socks-proxy](https://github.com/robinaa/docker-wireguard-socks-proxy))

## Why?

This is arguably the easiest way to achieve "app based" routing. For example, you may only want certain applications to go through your WireGuard tunnel while the rest of your system should go through the default gateway. You can also achieve "domain name based" routing by using a [PAC file](https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling/Proxy_Auto-Configuration_(PAC)_file) that most browsers support.

## Usage

Preferably, using `start` in this repository:
```bash
start /your/openvpn/directory
```

`/your/openvpn/directory` should contain *one* OpenVPN `.conf` file. It can reference other certificate files or key files in the same directory.

Alternatively, using `docker run` directly:

```bash
docker run -it --rm --device=/dev/net/tun --cap-add=NET_ADMIN \
    --name openvpn-client \
    --volume /your/openvpn/directory/:/etc/openvpn/:ro -p 1080:1080 \
    robinaa/openvpn-client-socks
```

Then connect to SOCKS proxy through through `localhost:1080` / `local.docker:1080`. For example:

```bash
curl --proxy socks5h://local.docker:1080 ipinfo.io
```

## HTTP Proxy

You can easily convert this to an HTTP proxy using [http-proxy-to-socks](https://github.com/oyyd/http-proxy-to-socks), e.g.

hpts -s 127.0.0.1:1080 -p 8080
