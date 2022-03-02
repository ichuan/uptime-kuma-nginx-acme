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
