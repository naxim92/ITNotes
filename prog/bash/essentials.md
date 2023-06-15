#bash

- `help some_command` - Получить help по built-in коммандам баша
- Переменная `STR="Hello World!"`
- `echo $STR`
- Выполнить команду в фоновом режиме (nohup - ПО работает даже после закрытия терминала) #background
```
nohup tar xjf archive &
```

- Если надо узнать соурс симлинка можно использовать `readlink -f /path/to/symlink`
```
# readlink -f /etc/letsencrypt/live/loacl/cert.pem
/etc/letsencrypt/archive/local/cert1.pem
```
- Узнать PID
	`echo $$`

## Подстановка переменных
#var #null
https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html
- `${parameter:-word}` - будет *подставлено* дефолтное значение, значение parameter не изменится (не присвоится)
- `${parameter:=word}` - будет *присвоено* дефолтное значение
- `${parameter:?word}` - значение word (может быть строкой) будет выведено в stderr
- `${parameter:+word}` - если parameter *не пустой*, то будет подставлено word
#array
- `${parameter:offset[:length]}` - выведет length элементов по смещению offset, parameter может быть строкой, offset может быть отрицательным
- `${!prefix*}`
- `${!prefix@}`
- `${!name[@]}`
- `${!name[*]}`
- `${#parameter}`
- `${parameter#word}`
- `${parameter##word}`
- `${parameter%word}`
- `${parameter%%word}`
- `${parameter/pattern/string}`
- `${parameter//pattern/string}`
- `${parameter/#pattern/string}`
- `${parameter/%pattern/string}`
- `${parameter^pattern}`
- `${parameter^^pattern}`
- `${parameter,pattern}`
- `${parameter,,pattern}`
- `${parameter@operator}`

## Сравнения
#eq #equals #ccomparison

Double parenthesis `(( ... ))` is used for arithmetic operations.
Double square brackets `[[ ... ]]` can be used to compare and examine numbers (only integers are supported)
- Строки
	- `string1 = string2` and `string1 == string2` - The equality operator returns true if the operands are equal.
		- Use the `=` operator with the `test` `[` command.
		- Use the `==` operator with the `[[` command for pattern matching.
	- `string1 != string2` - The inequality operator returns true if the operands are not equal.
	- `string1 =~ regex`- The regex operator returns true if the left operand matches the extended regular expression on the right.
	- `string1 > string2` - The greater than operator returns true if the left operand is greater than the right sorted by lexicographical (alphabetical) order.
	- `string1 < string2` - The less than operator returns true if the right operand is greater than the right sorted by lexicographical (alphabetical) order.
	- `-z string` - True if the string length is zero.
	- `-n string` - True if the string length is non-zero.
- Числа
	
	- `NUM1 -eq NUM2` returns true if NUM1 and NUM2 are numerically equal.
	- `NUM1 -ne NUM2` returns true if NUM1 and NUM2 are not numerically equal.
	- `NUM1 -gt NUM2` returns true if NUM1 is greater than NUM2.
	- `NUM1 -ge NUM2` returns true if NUM1 is greater than or equal to NUM2.
	- `NUM1 -lt NUM2` returns true if NUM1 is less than NUM2.
	- `NUM1 -le NUM2` returns true if NUM1 is less than or equal to NUM2.
