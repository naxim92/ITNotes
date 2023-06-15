#gitlab #cicd
```gitlab
# Описываем стейджы
stages:
	- stage_1
	- stage_2

# Объявляем ЛОКАЛЬНЫЕ переменные
variables:
	VAR: bla

stage_one:
	stage: stage_1 # Стейдж указываем явно
	tags:
		- test
	script:
		- action 1
		# Здесь переменная внутри команды так что ее надо "раскавычить"
		- bash -c 'echo '$VAR'_TEST_' 
	artifatcs:
		paths:
			# Просто юзаем переменную
			- build/$VAR/
	...
```
