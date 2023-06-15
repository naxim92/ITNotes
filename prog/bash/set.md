#bash #set

- `set [+abefhkmnptuvxBCEHPT] [(+|-o) option-name] [--] [-] [argument …]`
- Просто `set` отображает *shell переменные*
- `-opt` - включает опцию
- `+opt` - выключает опцию
- `-e` errexit - Прерывает работу сценария при появлении первой же ошибки (когда команда возвращает ненулевой код завершения)
- `-u` nounset -  При попытке обращения к неопределенным переменным, выдает сообщение об ошибке и прерывает работу сценария
- `-n` noexec - Читает и парсит команды из скрипта, **но не исполняет их**. Полезно для проверки сценария на предмет синтаксических ошибок. Игнорируется интерактивными оболочками.
- `-x` xtrace - Режим отладки. Перед выполнением команды печатает её со всеми уже развернутыми подстановками и вычислениями.
- `-T` functrace - DEBUG- и RETURN-ловушки будут унаследованы последующими средами. Аналогично как  `-E` для `ERR` -ловушек.
- `-` - Означает "конец опций" - все следующие аргументы будут рассматриваться как позиционные параметры, даже если начинаются с минуса, как опции.
- `--` - If no arguments follow, the positional parameters are unset. With arguments, the positional parameters are set, even if the strings begin with a `-` (dash) like an option.
- `pipefail` - If set, the exit code from a pipeline is different to the normal ("last command in pipeline") behaviour: `TRUE` when no command failed, `FALSE` when something failed (code of the rightmost command that failed)

- Note that if you have `set -e` set at the top of your script and your `return 1` or any other number besides 0, your entire script will exit #return #exit
- `trap 'log "($?) on line:$LINENO" "ERROR"' ERR` - Порядок кавычек ТОЛЬКО такой, иначе переменные неправильно расскроются (SC2064) #trap #quotes