
30-09-2025 13:46

Status: #baby

Tags: [[Java+]]

---
# Merge Conflicts - Git


Это происходит, когда разные изменения сделаны в одной и той же части файла или, когда один из разработчиков удалил файл, который другой изменил. О том, как решать конфликты при слиянии можно найти [здесь](https://youtu.be/Sqsz1-o7nXk).


1 тип -- automerger -  не будет сам мерджить, если файл, в котором возникает merge conflict - не закоммичен
	
	Решение:
	- git add .
	- git stash
	
	

2 тип:
```bash
PS C:\Users\нео\IdeaProjects\JDBC sample> git merge main
Auto-merging src/main/java/Launcher.java
CONFLICT (content): Merge conflict in src/main/java/Launcher.java
Automatic merge failed; fix conflicts and then commit the result.
	
PS C:\Users\нео\IdeaProjects\JDBC sample> git log --merge --oneline
0be36e4 (main) Added some main comment
b5efbc1 (HEAD -> Dev) Added some Dev comments
	
#только после получения ошибки слияния
git log --merge --oneline #список коммитов, содержащих merge conflicts
0be36e4 (main) Added some main comment
b5efbc1 (HEAD -> Dev) Added some Dev comments

#Просмотр статуса после merge conflict
PS C:\Users\нео\IdeaProjects\JDBC sample> git status
On branch Dev
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   src/main/java/Launcher.java

no changes added to commit (use "git add" and/or "git commit -a")
```


~={orange}Пример выделения места с merge conflict в файле:=~
```java
...
        resultSet.close();
<<<<<<< HEAD (current change)
        connection.close(); //some Dev commit
=======
        connection.close(); //some main comment
>>>>>>> main (incoming change)

    }
```

~={orange}Для решения merge conflict - вручную удаляем эти выделители и оставляем только ту строку кода, которую мы хотим оставить в результате=~

- сохраняем файл
- добавляем в индекс:
 ```bash
 PS C:\Users\нео\IdeaProjects\JDBC sample> git add .
  ```
  - коммитим:
  ```java
  PS C:\Users\нео\IdeaProjects\JDBC sample> git commit -m "finalizing the merge"
[Dev a91ffe6] finalizing the merge
  ```

### Отмена слияния

- `git merge --abort`


----
#### [[Merge Conflicts - Git - Flashcards|Link to flashcards]]



---
### References:

- [[Git]]
- [[Branching - Git]]