#docker

### Копировать файлы из контейнера на хост
```
docker cp <containerId>:/file/path/within/container /host/path/target
```

### Перенос строки в docker-compose
 ```
 container_name:
	....
    command: >-
	    long command
    ```