#git 

Подмодули позволяют вам сохранить один Git-репозиторий, как подкаталог другого Git-репозитория. Это даёт вам возможность клонировать в ваш проект другой репозиторий, но коммиты при этом хранить отдельно.

- Как добавить submodule
	``` git
	git submodule add <URL> <catalog-where-to-clone>
	```
- Можно пульнуть все сабмодули в проекте
	`git submodule foreach git pull origin master`
- Чтобы сделать pull в основном проекте с сабмодулями
	``` git
	git pull
	git submodule update --init --recursive
	```
- Best practice добавлять хуки на [post-merge](https://git-scm.com/docs/githooks#_post_merge)
	``` bash
	cd ~/main-project  
	echo "git submodule update --init --recursive" >> .git/hooks/post-merge
	```
- Чтобы склонировать вместе с сабмодулями
	`git clone --recursive <MAIN_PROJECT_REPO>`
- Можно сконфигурировать git, чтобы показывать diff статус сабмодулей
	`git config status.submodulesummary 1`