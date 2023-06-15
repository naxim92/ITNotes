Флаги
`last`
завершает обработку текущего набора директив модуля `ngx_http_rewrite_module`, после чего ищется новый location, соответствующий изменённому URI;
`break`
завершает обработку текущего набора директив модуля `ngx_http_rewrite_module` аналогично директиве [break](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#break);
(директива break внутри локейшена прервет обработку рерайтов и продолжит обработку запроосов в *этом* локейшене)
`redirect`
возвращает временное перенаправление с кодом 302; используется, если заменяющая строка не начинается с “`http://`”, “`https://`” или “`$scheme`”;
`permanent`
возвращает постоянное перенаправление с кодом 301.


Пример:

```
server {
...
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 last;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  last;
    return  403;
    ...
}
```


Если же эти директивы поместить в location “`/download/`”, то нужно заменить флаг `last` на `break`, иначе nginx сделает 10 циклов и вернёт ошибку 500:

```
 location /download/ {
     rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 break;
     rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  break;
    return  403;
 }
```