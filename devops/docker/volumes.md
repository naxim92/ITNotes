## Create volume
`docker volume create --name project_name__container_name`

Если докер создает volume автоматом, то volume именуется как:
- `project_name__container_name`
- если внутри контейнера назначен `container_name` , то имя создаваемого volume `container_name`