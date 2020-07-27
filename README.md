# point-guard

A simple NGINX reverse proxy using Docker.

### Proof of concept

#### Usage

Add the following entires to your `hosts` file

```
127.0.0.1 foo.test
127.0.0.1 bar.test
127.0.0.1 baz.test
```

Run PointGuard
```
$ docker-compose up
```

Make test requests
```
$ curl foo.test
> I'm 3dd0f7ffc576

$ curl bar.test
> I'm 80a0ebac6706

$ curl baz.test
> I'm 5f8e900bce0e
```

#### Adding a new site

Add a new entry to the `services` block:

```
# docker-compose.yml

mycoolapp:
    image: <YOUR IMAGE>
    ports:
      - 8080:80
```

Add a new `server` block to `reverse_proxy/nginx.conf`:
```
server {
    listen 80;
    server_name mycoolapp.test;

    location / {
        proxy_pass          http://mycoolapp:80;
        proxy_set_header    X-Forwarded-For $remote_addr;
    }
```
Note that in the `proxy_pass` directive, `mycoolapp` is the name of your service in the `docker-comose` file, and `80` is the port exposed by your service's docker container (_not_ your host port).
In other words, use the following format for the `proxy_pass` value: `http://<container-name>:<docker-port>`


You should now be able to access your app via:

```
$ curl mycoolapp.test
```