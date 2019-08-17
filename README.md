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
- `cluster-proxies`
  - This follows the same symantics as `proxies` but is extended to handle load balancing and health checks. Find an example below.

Ex:
```yaml
proxies:
  - subdomain: grafana
    host: localhost
    port: 3000
    allowed_cidr: 192.168.1.0/24 # optional
```

This produces a subdomain like grafna.your-home.duckdns.org, for example.

### Cluster proxies

If you're like me and you're running a compute cluster in your house, then then simple `poxies` object above might not be enough to accomodate the ephemeral nature of containers. For this reason there also exists a `cluster-proxies` object, which sets up a subdomain with multiple upstreams that nginx will health check and load balance between.

Ex:
```yaml
cluster-proxies:
  - subdomain: tasmota
    hosts:
      - 192.168.1.0
      - 192.168.1.1
      - 192.168.1.2
    port: 30001
```

Here, the `port` is assumed to be exposed on all of the `servers`.

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
