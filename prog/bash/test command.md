#bash #ccomparison #eq #equals 

### Оператор test
```sh
test EXPRESSION
[ EXPRESSION ] # старый оператор, поддерживается везде
[[ EXPRESSION ]] # новый оператор, в современных системах поддерживается нормально
```
Это прям команда, т.е. отдельный бинарник. Выполняется в отдельном процессе, bash ждет его код выхода.

Команда проверяет
```bash
FILE=/etc/resolv.conf
if test -f "$FILE"; then
    echo "$FILE exists."
else 
    echo "$FILE does not exist."
fi
```
Можно в одну строчку
``` bash
[ -f /etc/resolv.conf ] && echo "$FILE exists."
```
Или так
``` bash
[ ! -f /etc/resolv.conf ] && echo { "$FILE does not exist." } || echo "$FILE exists." 
```
Можно тестировать сразу несколько файлов (оператор AND)
```sh
if [ -f /etc/resolv.conf -a -f /etc/hosts ]; then
    echo "Both files exist."
fi
```

Или так

```sh
if [[ -f /etc/resolv.conf && -f /etc/hosts ]]; then
    echo "Both files exist."
fi
```

Разные операции
-   `-b` `FILE` - True if the FILE exists and is a special block file.
-   `-c` `FILE` - True if the FILE exists and is a special character file.
-   `-d` `FILE` - True if the FILE exists and is a directory.
-   `-e` `FILE` - True if the FILE exists and is a file, regardless of type (node, directory, socket, etc.).
-   `-f` `FILE` - True if the FILE exists and is a regular file (not a directory or device).
-   `-G` `FILE` - True if the FILE exists and has the same group as the user running the command.
-   `-h` `FILE` - True if the FILE exists and is a symbolic link.
-   `-g` `FILE` - True if the FILE exists and has set-group-id (`sgid`) flag set.
-   `-k` `FILE` - True if the FILE exists and has a sticky bit flag set.
-   `-L` `FILE` - True if the FILE exists and is a symbolic link.
-   `-O` `FILE` - True if the FILE exists and is owned by the user running the command.
-   `-p` `FILE` - True if the FILE exists and is a pipe.
-   `-r` `FILE` - True if the FILE exists and is readable.
-   `-S` `FILE` - True if the FILE exists and is a socket.
-   `-s` `FILE` - True if the FILE exists and has nonzero size.
-   `-u` `FILE` - True if the FILE exists, and set-user-id (`suid`) flag is set.
-   `-w` `FILE` - True if the FILE exists and is writable.
-   `-x` `FILE` - True if the FILE exists and is executable.
-   `-z` `STRING` - Returns true if string _string_ is empty, i.e., **""**.
-   `-o` `OPTION` - Returns true if the shell option _opt_ is enabled.