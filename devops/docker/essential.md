#docker  #cmd #entrypoint
https://docs.docker.com/engine/reference/builder/#entrypoint

# ENTRYPOINT vs CMD
### Как устроены ENTRYPOINT и CMD

```
ENTRYPOINT ["executable", "param1", "param2"] # "exec" format
ENTRYPOINT command param1 param2 # "shell" format
```
==(!)== **Режим _exec_ является рекомендуемым**
``` exec format
PID   USER     TIME   COMMAND  
    1 root       0:00 ping www.google.com
```
``` shell format
PID   USER     TIME   COMMAND  
    1 root       0:00 /bin/sh -c ping www.google.com
```
`CMD` работает аналогично.
Рекомендации связаны с тем, что контейнеры задуманы так, чтобы содержать один процесс. **Например, отправленные в контейнер сигналы перенаправляются процессу, запущенному внутри контейнера с идентификатором PID, равным 1.**

### Помогите, я не могу выйти!
Сигнал `SIGINT`, который был направлен процессу `sh`, не будет перенаправлен в подпроцесс `ping`, и оболочка не завершит работу. Если по какой-то причине вы действительно хотите использовать режим _shell_, выходом из ситуации будет использовать `exec` для замены процесса оболочки процессом `ping`.
```
$ cat Dockerfile
FROM alpine  
ENTRYPOINT exec ping www.google.com  
```

Проблема запуска НЕ в режиме оболочки заключается в том, что вы не можете воспользоваться преимуществами ==(!)== переменных среды (таких как `$PATH`) и прочими возможностями ( например \*, тк ==(!)== символы подстановки интерпретируются самой оболочкой), которые предоставляет использование оболочки.
![[docker_cmd_entrypoint.png]]

-  Если вы используете режим _shell_ для `ENTRYPOINT`, `CMD` игнорируется.
- При использовании режима _exec_ для `ENTRYPOINT` аргументы `CMD` добавляются в конце.
- При использовании режима _exec_ для инструкции `ENTRYPOINT` необходимо использовать режим _exec_ и для инструкции `CMD`. Если этого не сделать, Docker попытается добавить `sh -c` в уже добавленные аргументы, что может привести к некоторым непредсказуемым результатам.
- Инструкции `ENTRYPOINT` и `CMD` могут быть переопределены с помощью флагов командной строки.
```
docker run --entrypoint [my_entrypoint] test # Override ENTRYPOINT
docker run test [command 1] [arg1] [arg2]  # Override CMD
```

### Dockerfile

Используйте `ENTRYPOINT`, если вы не хотите, чтобы разработчики изменяли исполняемый файл, который запускается при запуске контейнера. Вы можете представлять, что ваш контейнер – _исполняемая оболочка_. Хорошей стратегией будет определить _стабильную_ комбинацию параметров и исполняемого файла как `ENTRYPOINT`. Для нее вы можете (не обязательно) указать аргументы `CMD` по умолчанию, доступные другим разработчикам для переопределения.
``` Dockerfile
FROM alpine  
ENTRYPOINT ["ping"]  
CMD ["www.google.com"]  
```

```
$ docker run test
PING www.google.com (172.217.7.164): 56 data bytes  
64 bytes from 172.217.7.164: seq=0 ttl=37 time=0.306 ms
```
Переопределение CMD собственными параметрами:
```
$ docker run test www.yahoo.com
PING www.yahoo.com (98.139.183.24): 56 data bytes  
64 bytes from 98.139.183.24: seq=0 ttl=37 time=0.590 ms 
```


Используйте только `CMD` (без определения `ENTRYPOINT`), если требуется, чтобы разработчики могли легко переопределять исполняемый файл. Если точка входа определена, исполняемый файл все равно можно переопределить, используя флаг `--entrypoint`. Но для разработчиков будет гораздо удобнее добавлять желаемую команду в конце строки `docker run`.
``` Dockerfile
FROM alpine  
CMD ["ping", "www.google.com"]  
```