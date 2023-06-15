#bash

- *case*
https://www.opennet.ru/docs/RUS/bash_scripting_guide/x5210.html
	``` bash
	case "$variable" in  
		"$condition1" )  
		command;;
		"$condition2" )  
		command...  
	 ;;  
	esac
	```
- *getopt* (external)
	``` bash
	getopt
```
- *getopts* (built-in)
	`getopts` does not support long arguments with values (like '--foo=bar')
	``` bash
	getopts getopts optstring name [arg …]
	```
	- OPTSTRING – Список символов, которые распознаются в качестве аргументов. Пример: -h, -v.
	- OPTNAME – Разобранный аргумент из $@ будет сохранен в переменной OPTNAME. Вы можете использовать любое имя в качестве optname.
	- OPTARG – Если были переданы дополнительные аргументы, то они сохраняются в переменной OPTARG.
	- OPTIND – Индекс, указывающий на следующий обрабатываемый аргумент.
	- Добавление двоеточия в начало OPTSTRING подавит сообщения об ошибках по умолчанию.
	- `:` - Если не передан дополнительный аргумент, OPTARG будет установлен на двоеточие, и вы можете написать логику для вывода сообщений об ошибках.
	- `?` - Если встретилась некорректная опция, то в переменную имя помещается ?.
