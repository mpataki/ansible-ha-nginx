# ansible-ha-nginx

This ansible role sets up a [nginx](https://www.nginx.com/) server to reverse proxy the home assistant web UI's HTTP and Websocket ports, as well as grafana. It assumes you've set up [let's encrypt](https://github.com/mpataki/ansible-ha-letsencrypt) for SSL encryption and will handle HTTP -> HTTPS redirects.

Additionally, it (soon to be optionally) will setup a server for hosting HLS streams from the Pi, which can be used by streaming devices to display live video feeds (like those produced by projects like [this](https://github.com/EliasKotlyar/Xiaomi-Dafang-Hacks), among others).

## Requirements

Really this should work on any debian based system, but has been tested on a Raspberry Pi running Hassbian.

## Role Variables

- `domain_name`
  - Ex. `my_home.duckdns.org`
  - This is the domain name where your pi can be reached from the internet.

## Dependencies

- [ha-letsencrypt](https://github.com/mpataki/ansible-ha-letsencrypt) for SSL. This can be installed via git or via ansible galaxy.

## Example Playbook

```yml
    - hosts: pi
      vars:
        domain_name: my_home.duckdns.org
      roles:
         - role: mpataki.ha-nginx
```

## License

MIT
