#git 

1. Откатить файл до состаяния в определенном коммите
git checkout 3f25701874aa5a2c16cc6275fa30baa5b6e477a6 doc/text.html

2. Красивая история
git log --decorate=full --graph --all --name-only --pretty=fuller

2. Посмотреть изменения в коммите
git diff c674264~ c674264

3. Удалить ветку
git branch -d task-59428
git push origin --delete task-59428

4. Посмотреть красивый граф
git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all

5. Смерджить ветку в мастер
git checkout task-59428
git status
git checkout master
git pull
git checkout task-59428
git rebase master
git checkout master
git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --all
git merge task-59428
git push master
