# ansible-ha-nginx

[![Build Status](https://travis-ci.org/mpataki/ansible-ha-nginx.svg?branch=master)](https://travis-ci.org/mpataki/ansible-ha-nginx)

This ansible role sets up a [nginx](https://www.nginx.com/) server to reverse proxy the home assistant web UI's HTTP and Websocket ports. It assumes you've set up [let's encrypt](https://github.com/mpataki/ansible-ha-letsencrypt) for SSL encryption and will handle HTTP -> HTTPS redirects.

## Requirements

Really this should work on any debian based system, but has been tested on a Raspberry Pi running Hassbian.

## Role Variables

- `domain_name`
  - Ex. `my_home.duckdns.org`
  - This is the domain name where your pi can be reached from the internet.
- `proxies` is a list of objects following the form below, each of which describe subdomain configuration.
  - In addition, nginx will handle SSL termination and redirection from http to https.
  - The optional `allowed_cidr` key will make nginx enforce that traffic to this subdomain must come from the provided cidr block. The example below only allows the subdomain to be accessed from the local network.

Ex:
```yaml
proxies:
  - subdomain: grafana
    host: localhost
    port: 3000
    allowed_cidr: 192.168.1.0/24 # optional
```

This produces a subdomain like grafna.your-home.duckdns.org, for example.

## Dependencies

- [ha-letsencrypt](https://github.com/mpataki/ansible-ha-letsencrypt) for SSL. This can be installed via git or via ansible galaxy.

## Example Playbook

```yml
    - hosts: pi
      vars:
        domain_name: my_home.duckdns.org
        proxies:
          - subdomain: grafana
            host: localhost
            port: 3000
      roles:
         - role: mpataki.ha-nginx
```

## License

MIT
