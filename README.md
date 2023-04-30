# Hugo

This consists of a containerized [Hugo](https://gohugo.io) project with the [Bookworm](https://github.com/gethugothemes/bookworm-light) theme, intended for easy deployment into an environment with an existing reverse proxy.

# Usage

Clone the repo:

`git clone https://git.gomarcd.dev/gomarcd/digipom-website`

The `docker-compose.yml` includes:

- [klakegg/hugo](https://hub.docker.com/r/klakegg/hugo/)
- [nginx](https://hub.docker.com/_/nginx/)

`klakegg/hugo` will build the Hugo site files into `./public` when you start the stack and `nginx` will serve the site on port 8080. If you want it to use a different port, edit `docker-compose.yml` accordingly:

```
version: '3'

services:
  hugo-build:
    image: klakegg/hugo:latest-ext
    volumes:
      - ".:/src"

  hugo-webserver:
    image: nginx:alpine
    container_name: "digipom-website"
    restart: always
    depends_on:
      - hugo-build
    volumes:
      - ./public:/usr/share/nginx/html
    ports:
      - "8222:80"
```

Edit `config.toml` as needed, ensure value of `baseURL = "https://digihugo.gomarcd.dev"` matches the real domain name.

When ready, do `docker compose up -d` to start the stack.

Do `docker compose logs -f` to check logs. You should see something like this:

```
[+] Running 3/3
 ✔ Network digipom-website_default         Created                                                                                                                      0.2s 
 ✔ Container digipom-website-hugo-build-1  Started                                                                                                                      1.2s 
 ✔ Container digipom-website               Started                                                                                                                      1.9s 
digipom-website  | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
digipom-website  | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
digipom-website  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
digipom-website  | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
digipom-website  | 10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
digipom-website  | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
digipom-website-hugo-build-1  | Start building sites … 
digipom-website-hugo-build-1  | hugo v0.107.0-2221b5b30a285d01220a26a82305906ad3291880+extended linux/amd64 BuildDate=2022-11-24T13:59:45Z VendorInfo=hugoguru
digipom-website-hugo-build-1  | 
digipom-website-hugo-build-1  |                    | EN  
digipom-website-hugo-build-1  | -------------------+-----
digipom-website-hugo-build-1  |   Pages            | 55  
digipom-website-hugo-build-1  |   Paginator pages  |  1  
digipom-website-hugo-build-1  |   Non-page files   |  0  
digipom-website-hugo-build-1  |   Static files     |  0  
digipom-website-hugo-build-1  |   Processed images | 58  
digipom-website-hugo-build-1  |   Aliases          |  1  
digipom-website-hugo-build-1  |   Sitemaps         |  1  
digipom-website-hugo-build-1  |   Cleaned          |  0  
digipom-website-hugo-build-1  | 
digipom-website-hugo-build-1  | Total in 697 ms
digipom-website               | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
digipom-website               | /docker-entrypoint.sh: Configuration complete; ready for start up
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: using the "epoll" event method
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: nginx/1.23.3
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r4) 
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: OS: Linux 5.15.0-70-generic
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker processes
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 30
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 31
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 32
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 33
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 34
digipom-website               | 2023/04/30 22:04:28 [notice] 1#1: start worker process 35
```

To make sure it's running, do `docker ps | grep digipom-website`:

```
399a03b95eda   nginx:alpine                               "/docker-entrypoint.…"   About a minute ago   Up About a minute      0.0.0.0:8080->80/tcp                                digipom-website
```

To update the site with any changes recently pushed to the repo, do `git pull` and `docker compose up -d`.