#gitlab 

- CI\\CD variables могут определяться на уровне группы. Важно, что имеется ввиду **группа, в которую входит проект.** Т.е. URL проекта `https://gitlab.server/<GROUP>/<PROJECT>` Ещё раз проговорю: логика работы с переменными на уровне проектов, а не пользователей. Нельзя сделать пользователя, добавить его в группу и настроить ему переменные. Можно создать в группе проект и в этой группе настроить переменные.
- Чтобы добавить переменные в группу надо посмотреть, в какие группы входит пользователь\\проект (не нужен админский доступ), провалиться в группу. Затем Settings -> CI\\CD -> Variables
![[gitlab-group-settings.png]]