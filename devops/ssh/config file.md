#ssh 

- Можно указать для каких ssh подключений, к каким хостам, какие настройки использовать. Полезно для #git 
	``` ~/.ssh/config
	Host my-gitlab.ru
	    Hostname my-gitlab.ru
	    User user%40domain.com
	    IdentityFile "/C/Security/my_gitlab_private.key"
	```
	Для Linux это файл `~/.ssh/config` для Windows `%username%\.ssh\config`
	В Windows путь до файла *IdentityFile* указывается как `"/E/bla/bla/id_rsa"`
	Подробнее [здесь](https://docs.gitlab.com/ee/user/ssh.html)
	В имени пользователя использовать вместо `@`  -> `%40`