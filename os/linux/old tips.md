#linux #bash

> Заметки со старой работы

1. Проверить, поднят ли физический линк в сет интерфейсе
cat /sys/class/net/eth0/operstate

2. Узнать размер страницы памяти
getconf PAGESIZE

3. Кто жрет проц:
ps -eo pcpu,pid,user,args | sort -k 1 -r | head -10

4. Посмотреть порядок сертификатов в цепочке
openssl crl2pkcs7 -nocrl -certfile ~/ansible/roles/ssl.install/files/etc/nginx/ssl/retailcrm_public_chained.crt | openssl pkcs7 -print_certs -noout

5. Просмотреть инфу о сертификате
openssl x509 -in roles/ssl.install/files/etc/nginx/ssl/retailcrm_es_public_chained.crt -text -noout

6. Сколько весит папка
sudo du -h --max-depth=1 /

7. Как прочитать кор Дамп
gdb /usr/sbin/nginx /var/spool/abrt/ccpp-2021-01-19-13:16:04-2843/coredump
(gdb) bt
#0  0x000055e11404ac08 in ngx_ssl_recv (c=0x7fcf829a7158,............

8. Как найти и убить процесса
sudo kill -9 $(sudo ps aux | grep "sudo docker run 918728908124 bash" | awk '{print $2}')

9. Посмотреть инфу о systemctl сервисе
systemctl show node.service -p TimeoutStartSec
или так
systemctl show node.service

10. Скачать сертификат с сайта
openssl s_client -showcerts -servername www.simla.com -connect www.simla.com:443 < /dev/null > roles/ssl.install/files/etc/nginx/ssl/simla_com_public.crt

11. Направить в буффер обмена
| xclip -sel clipboard

12. Centos - посмотреть какой пакет нам нужен
yum whatprovides netstat

13. Прослушать php-fpm трафф
sudo tcpdump -i any port 9000 -A | strings

14. Посмотреть инфу о лимитах на процесс
sudo cat /proc/5474/limits

15. Поднять лимит на открытые файлы (Max open files) для процесса (перезапуск не нужен)
sudo prlimit -n4096 -p 5474

16. Тоже самое что выше, только для сервиса типа постгрес
`for i in $(ps aux | grep -E 'postmaster|postgres:' | grep -v grep | awk '{print $$2;}'); do cat /proc/$i/limits | grep 'open files'; done`

17. Узнать, скомпилен ли nginx с поддержкой модуля
nginx -V 2>&1 | egrep --color -o 'realip_module'

18. Посмотреть xml без комментов
sudo yum install tidy
tidy -quiet -asxml -xml -indent -wrap 1024 --hide-comments 1 /tmp/config.xml | less
Посмотреть xml по конкретному узлу
xmllint --xpath "/clickhouse/listen_host" /tmp/config.xml
