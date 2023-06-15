- В nginx контейнер можно прокинуть env переменные.
```/etc/nginx/template/service_conf_template
server_name ${NGINX_HOST};
```

``` docker-compose
    environment:
      - NGINX_HOST=${NGINX_HOST:-wireguard.local}
```
