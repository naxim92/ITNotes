#nginx

-  Регулярка для *безусловного* добавления в конец URI слэша
```
rewrite ^([^.]+[^/])$ $1/ permanent;
```

- Проверить, существует ли файл (аналогично в [[prog/bash/recipes]])
```
if (-f $request_filename) {
	break;
}
```
Проверить отсутсвие файла
```
if (!-e $request_filename) {
	rewrite ^(.+)$ /index.php?q=1 last;
}
```