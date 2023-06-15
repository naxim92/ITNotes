#docker #powershell 

- Удалить все контейнеры
	`docker ps -a --format "{{.ID}}" | ForEach-Object -Process {docker stop $_ }`