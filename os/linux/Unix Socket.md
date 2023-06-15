#linux #unix_socket

### Послушать сокет можно с помощью утилиты socat, сделав перенаправление сокетов
``` bash
sudo apt install socat
socat -v UNIX-LISTEN:/tmp/socat-listen UNIX-CONNECT:/path/to/real.socket
```