
Theory for the cards: [[Branching - Git]]

FILE TAGS: git java+

Q: В чём разница между `git merge` и `git rebase`?
A: `git merge <some_branch>` - делается из ветки, в которой хотим продолжить разработку (обычно из child - в parent).
Он переносит все коммиты `<some_branch>` в начало текущей ветки. 
	
`git rebase <branch_to_be_rebased>` - слияние веток, все коммиты ветки `<branch_to_be_rebased>` присоединяются в начало текущей ветки, при возникновении merge конфликтов - они решаются вручную. 
	
Ветка `<branch_to_be_rebased>` - НЕ удаляется, она принимает в себя коммиты parent ветки и всё.
	
Короче:
	
- **Merge**: combines histories (preserves original structure).
    
- **Rebase**: _rewrites history_ so it looks like you started from the latest version of the base branch.
    
- Both can cause conflicts, which you resolve the same way.
<!--ID: 1759738534435-->


Q: Что делает `git cherry pick`?
A: `git cherry pick <commit_index>` - позволяет брать коммит из child branch и добавлять его в начало текущей ветки. 
	
Все конфликтs решаются как merge conflicts.
<!--ID: 1759738534442-->
