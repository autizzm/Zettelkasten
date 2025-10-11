
Theory for the cards: [[Git]]

FILE TAGS: git

Q: Как удалить определённый файл из индекса?
A: `git reset <path_to_file>`
> [!warning] **Не путать с `git reset --soft/--hard <commit>`**
> `git reset --soft <commit>` - удалит коммиты после указанного, но оставит все файлы, которые изменились после этого коммита
> 
> `git reset --hard <commit>` - удалит все коммиты до указанного, а также все изменения
<!--ID: 1760205703539-->
