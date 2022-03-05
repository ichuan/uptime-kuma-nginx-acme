# uptime-kuma-nginx-acme

Like in the name, this is a combination of [uptime-kuma](https://github.com/louislam/uptime-kuma), nginx and [acme.sh](https://github.com/acmesh-official/acme.sh), with SSL-auto-renewing function, as a docker compose project.


## Setup

Assume your domain is `uptime.mysite.com`.

- Create nginx site config file: `cp config/nginx/conf.d/web.conf.example config/nginx/conf.d/web.conf`
- Replace all `uptime.example.com` with `uptime.mysite.com` in `config/nginx/conf.d/web.conf` file
- Initial start: `docker-compose up -d`
- Getting certs: `docker-compose exec acme --issue -d uptime.mysite.com --webroot /fake-www --server letsencrypt`
- Comment out fake ssl config (and uncomment the real ones) in `config/nginx/conf.d/web.conf`
- Stop it: `docker-compose down`


## Adding SMTP relay support

To send notifications via SMTP you can use a local Postfix working as relay. You'll also
need a service like Sendgrid, AWS SES or similar to do the final delivery.

To use the local SMTP you'll have to:

- Create the local docker compose override: `cp docker-compose.override.yml.example docker-compose.override.yml`
- Configure it with your relay host credentials, see: [https://hub.docker.com/r/alterrebe/postfix-relay/](https://hub.docker.com/r/alterrebe/postfix-relay/)
- Setup the uptime-kuma Email (SMTP) notification using `smtp` as Hostname, Port `25`, no security and no username/password

## Start

```
docker-compose up -d
```


## Stop

```
docker-compose down
```


## Integrate with docker swarm

Edit `docker-compose.yml`, change network section to:

```
networks:
  uptime-net:
    external: true
    driver: overlay
```

, where `uptime-net` is your network in swarm. And change your `container_name` in need to avoid name collision.
