
06-10-2025 11:19

Status: #baby - don't know if I should make flashcards

Tags: [[Java+]] [[Git]]

---
# Conventional commits - Git

The commit message should be structured as follows:

---

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

---

The commit contains the following structural elements, to communicate intent to the consumers of your library:

1. **fix:** a commit of the _type_ `fix` patches a bug in your codebase 
	
2. **feat:** a commit of the _type_ `feat` introduces a new feature to the codebase
	
3. **BREAKING CHANGE:** a commit that has a footer `BREAKING CHANGE:`, or appends a `!` after the type/scope, introduces a breaking API change. A BREAKING CHANGE can be part of commits of any _type_.
	
4. _types_ other than `fix:` and `feat:` are allowed, for example [@commitlint/config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional)recommends `build:`, `chore:`, `ci:`, `docs:`, `style:`, `refactor:`, `perf:`, `test:`, and others.
	
5. _footers_ other than `BREAKING CHANGE: <description>` may be provided and follow a convention similar to [git trailer format](https://git-scm.com/docs/git-interpret-trailers).


> [!note]
> A scope MAY be provided after a type. A scope MUST consist of a noun describing a section of the codebase surrounded by parenthesis, e.g., `fix(parser):`


---

## Examples

~={green}Commit message with description and breaking change footer=~

```
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

~={green}Commit message with `!` to draw attention to breaking change=~

```
feat!: send an email to the customer when a product is shipped
```

~={green}Commit message with scope and `!` to draw attention to breaking change=~

```
feat(api)!: send an email to the customer when a product is shipped
```

~={green}Commit message with both `!` and BREAKING CHANGE footer=~

```
chore!: drop support for Node 6

BREAKING CHANGE: use JavaScript features not available in Node 6.
```

~={green}Commit message with no body=~

```
docs: correct spelling of CHANGELOG
```
~={green}
Commit message with scope=~

```
feat(lang): add Polish language
```

~={green}Commit message with multi-paragraph body and multiple footers=~

```
fix: prevent racing of requests

Introduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.

Remove timeouts which were used to mitigate the racing issue but are
obsolete now.

Reviewed-by: Z
Refs: #123
```


----
#### [[Conventional commits - Git - Flashcards|Link to flashcards]]



---
### References:

