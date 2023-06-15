#systemctl

- Просмотреть все unit
	`systemctl list-units --all`
- Просмотреть unit файл
	`systemctl cat atd.service`
- Отредактировать unit файл
`systemctl edit nginx.service`
- Просмотреть зависимости юнита
	`systemctl list-dependencies sshd.service`
- Выключить сервис (замаскировать)
	`systemctl mask nginx.service`