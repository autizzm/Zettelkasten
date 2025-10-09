
06-10-2025 10:42

Status: #baby 

Tags: [[Java+]] [[Git]]

---
# Branching

#### Branch creation & switching

- `git branch <new_branch_name>` - добавление новой ветки
	
- `git checkout <branch_name>` - переход к новой ветке -> при создании коммита он будет прикреплен к новой ветке
(`git checkout -b <new_branch_name>` - создание + перемещение к новой ветке)

### Интеграция изменений

- `git merge <branch_to_be_merged_with_current_branch>`-  слияние двух веток и создание коммита

![[Pasted image 20250930133759.png]]

> [!note] 
> **`git merge <some_branch>` - добавит все коммиты ветки <some_branch> в текущую ветку**
> 
> Чтобы не добавлять все коммиты ветки `<some_branch>` (они ставятся после последнего коммита current branch и последним коммитом `<some_branch>`) можно использовать `--squash`:
> ```bash
> git merge --squash feature
> ```

### What happens step-by-step

Let’s say we start with this:
```mathematica
A---B---C  (main)
         \           
          D---E---F  (feature)
```

If you are on `main` and run:

```bash
git merge feature
```
Git does this:

- It **keeps all the existing commits** (`A, B, C, D, E, F`)
    
- Then creates a **new merge commit**, say `M`, that has **two parents** (`C` from `main` and `F` from `feature`).
    

Result:

```mathematica
A---B---C-------M  (main)
         \     /           
          D---E---F  (feature)
```

✅ **All commits stay in their original order.**  
Nothing is rewritten or moved “on top.”  
Git just joins the two histories together with the merge commit `M`.

---

`git rebase <branch_to_be_rebased>` - слияние веток, все коммиты ветки `<branch_to_be_rebased>` присоединяются в начало текущей ветки, при возникновении merge конфликтов - они решаются вручную. 

(Все конфликты решаются как [[Merge Conflicts - Git|merge conflicts]])

> [!warning]
> ветка `<branch_to_be_rebased>` - ~={green}НЕ удаляется=~
> 
> И parent branch  и current branch продолжат существовать, просто current branch теперь начинается с последнего коммита parent ветки.



![[Pasted image 20250930133900.png]]


### 🔹 What happens step-by-step

Let’s say your branches look like this:
```mathematica
A---B---C  (main)
         \           
          D---E---F  (feature)
```

If you run:

```bash
git checkout feature 
git rebase main
```

Git will:

1. Temporarily _remove_ commits `D`, `E`, and `F` (your branch commits).
    
2. Move your branch pointer to `main` (commit `C`).
    
3. Reapply each of your commits (`D`, `E`, `F`) **on top of C**, creating _new commits_ (`D'`, `E'`, `F'`).
    

Result:

```mathematica
`A---B---C---D'---E'---F'  (feature)`
```


### ✅ Diffrence between mege and rebase:

- **Merge**: combines histories (preserves original structure).
    
- **Rebase**: _rewrites history_ so it looks like you started from the latest version of the base branch.
    
- Both can cause conflicts, which you resolve the same way.


---

`git cherry-pick <commit_index>` - позволяет брать коммит из child branch и добавлять его в начало текущей ветки. 

(Все конфликты решаются как [[Merge Conflicts - Git|merge conflicts]])

![[Pasted image 20250930133949.png]]

---

![[Pasted image 20251006110153.png]]


### [[Merge Conflicts - Git|Merge Conflicts]]

----
#### [[Branching - Git - Flashcards|Link to flashcards]]



---
### References:

- [[Git]]
- 