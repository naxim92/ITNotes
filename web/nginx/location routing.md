#nginx #location #virtual_host

1. **server_name** не используется для выбора виртуального сервера, если есть более точное соответствие по директиве **listen**. При оценке совпадения по директиве **listen** порядок выбора блока виртуального сервера:
	- блок с точным ip адресом. ==Даже если есть другой блок с точным указанием **server_name** (!)==
	- блок с 0.0.0.0 или без указания ip адреса (подставляется дефолт 0.0.0.0:80). 

2. Порядок оценки **server_name** (по заголовку запроса **Host**):
	- точное совпадение (первое точное совпадение)
	- wildcard в начале **server_name** (самое длинное соответсвие) `server_name *.example.org;` vs `server_name *.org;`
	- wildcard в конце **server_name** (самое длинное соответсвие) `server_name www.example.*;
	- регулярки (первое совпадение) `server_name ~^(subdomain|www|host1).*\.example\.com$;`
	- дефолтный сервер
		- первый виртуальный сервер в конфигурации
		- **default_server** если объявлен такой блок

2. а.  Если запросы без поля “Host” в заголовке не должны обрабатываться, можно определить сервер, который будет их отклонять:
```
 server {
	listen      80;
     server_name "";
     return      444;
 }
```

3. Алгоритм выбора **location**:
	- если есть точное совпадение (**=**) - ==выбираем его==
	- определяем подходящие расположения по самому длинному совпадению префикса:
		- если есть подходящий блок с модификатором **^~** - ==выбираем его==
		- если есть подходящий блок без модификатора **^~** - просто сохраним его пока
	-  определяем подходящее расположение по регэкспам:
		- если расположения с регулярными выражениями будут найдены _внутри_ совпадающего расположения с самым длинным префиксом, Nginx переместит их наверх списка расположений с регулярными выражениями для проверки
		- проходим последовательно по локейшенам
		- первый блок с подходящим регэкспом -  ==выбираем его==
	- если ничего не найдено по регэкспам -  ==выбираем сохраненный ранее блок по префиксу==

Важно понимать, что по умолчанию Nginx будет отдавать совпадениям регулярных выражений приоритет перед совпадениями префиксов. Однако он вначале _оценивает_ расположения префиксов, позволяя администратору переопределить этот приоритет, используя модификаторы `=` и `^~` при определении расположения.

Наконец, важно понимать, что совпадения регулярных выражений _с_ самым длинным совпадением префикса будут иметь больший приоритет при оценке регулярных выражений Nginx. Они будут оцениваться по порядку до начала оценки любых других совпадений регулярных выражений.

Обычно, когда для обслуживания запроса выбирается блок расположения, запрос полностью обрабатывается в этом контексте, начиная с этого момента. Обработка запроса определяется только выбранным расположением и унаследованными директивами без вмешательства других родственных блоков расположения.
Т.е. при использовании в конфигах некоторых директив может происходить внутренний редирект (*поиск нужной локации заново*).

3. a Модуль **ngx_http_index_module**
Необходимо иметь в виду, что при использовании индексного файла делается внутреннее перенаправление и запрос может быть обработан уже в другом location’е. Например, в такой конфигурации:

```
location = / {
	 index index.html;
}
location / {
     ...
 }
```

запрос “`/`” будет фактически обработан во втором location’е как “`/index.html`”.

3. b Директива **try_files**
```
root /var/www/main;
location / {
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
```

В примере выше, если мы делаем запрос `/blahblah`, запрос получит первое расположение. Оно попытается найти файл с именем `blahblah` в каталоге `/var/www/main`. Если это не получится сделать, будет выполнен поиск файла с именем `blahblah.html`. Затем будет выполнен поиск каталога `blahblah/` в каталоге `/var/www/main`. Если все эти попытки закончатся неудачно, будет выполнена переадресация на `/fallback/index.html`. В этом случае будет активирован другой поиск расположения, который будет перехвачен вторым блоком расположения. Он выдаст файл `/var/www/another/fallback/index.html`.

3. c Директива **error_page**
```
root /var/www/main;

location / {
    error_page 404 /another/whoops.html;
}

location /another {
    root /var/www;
}
```

Каждый запрос, кроме начинающихся с `/another`, будет обрабатываться первым блоком, который будет выводить файлы из `/var/www/main`. Однако, если файл не будет найден (статус 404), будет выполнена внутренняя переадресация на `/another/whoops.html`, в результате чего будет активирован новый поиск расположения, который попадет на второй блок. Файл будет выводиться из `/var/www/another/whoops.html`.

3. d Модуль **ngx_http_rewrite_module**