# ansible-ha-nginx

[![Build Status](https://travis-ci.org/mpataki/ansible-ha-nginx.svg?branch=master)](https://travis-ci.org/mpataki/ansible-ha-nginx)

This ansible role sets up a [nginx](https://www.nginx.com/) server to reverse proxy the home assistant web UI's HTTP and Websocket ports. It assumes you've set up [let's encrypt](https://github.com/mpataki/ansible-ha-letsencrypt) for SSL encryption and will handle HTTP -> HTTPS redirects. This can be done via the mpataki.ha_lets_encrypt role if you'd like.

## Requirements

Really this should work on any debian based system, but has been tested on a Raspberry Pi running Hassbian.

## Role Variables

- `domain_name`
  - Ex. `my_home.duckdns.org`
  - This is the domain name where your pi can be reached from the internet.
- `proxies` is a list of objects following the form below, each of which describe subdomain configuration.
  - In addition, nginx will handle SSL termination and redirection from http to https.
  - The optional `allowed_cidr` key will make nginx enforce that traffic to this subdomain must come from the provided cidr block. The example below only allows the subdomain to be accessed from the local network.
- `nginx_config_dir` is a local directory where any custom sites file that you want copied to nginx's sites-enabled will be sourced from.
- `nginx_streams_config_dir` is a local directory where any custom stream comfigs (TCP or UDP proxies, for example) that you want nginx to include will be sourced from.

Ex:
```yaml
proxies:
  - subdomain: grafana
    host: localhost
    port: 3000
    allowed_cidr: 192.168.1.0/24 # optional
```

This produces a subdomain like grafna.your-home.duckdns.org, for example.

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
