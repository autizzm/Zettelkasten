
30-09-2025 10:49

Status: #baby

Tags: [[Java+]]

---
# SSH


Чтобы подключиться к серверу, знач его SSH credentialsнужно, чтобы на сервере был запущен SSHD (Open SSH Daemon)

```bash 
ssh brad@192.168.1.29
# brad - имя пользователя под которым входим
# 192.168.1.29 - ip адрес сервера
```

### Authentication methods

- password - опасно, подвержен брут форсу
	
- public / private key pair
	
	```bash
	#generating keys
	ssh-keygen
	```
	- `~/.ssh/id_.rsa` - private key
	- `~/.ssh/id_rsa.pub` - public key
	- public key goes into server **"authorized_keys"** file
	
- Host based - файл список хостов, которым разрешено подключение

----
#### [[SSH - Flashcards|Link to flashcards]]



---
### References:

