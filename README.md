# Установка и настройка:

1. Создаём 2 виртуальные машины (я создал две ubuntu 24.04 в AWS). Машины дальше я буду именовать как "server" и "host". Так как я использую AWS, то пользователями будут ubuntu. Также не забываем открыть нужные нам порты, как минимум 8080;

2. На server ставим сервер Jenkins (https://www.jenkins.io/doc/book/installing/linux/). Желательно начать с пункта про Java (он указан ниже), а потом уже к установке Jenkins;

3. На host ставим docker (https://docs.docker.com/engine/install/ubuntu/). Потом добавляем пользователя ubuntu в группу docker:
```
sudo usermod -aG docker ubuntu
newgrp docker
```

4. На server ставим ansible (https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu);

5. На server ставим docker (https://docs.docker.com/engine/install/ubuntu/). Потом добавляем пользователя jenkins в группу docker:
```
sudo usermod -aG docker ubuntu
newgrp docker
```

6. На server в /etc/ansible/hosts прописываем группу хостов для ansible. Создаём директорию /var/lib/jenkins/playbooks для плейбуков. Добавляем ansible-playbook в директорию /var/lib/jenkins/playbooks;

7. Переходим на веб-сервер jenkins (<ip_address:8080>), создаём Pipeline:
```
Build Triggers:
	Ставим галочку на GitHub hook trigger for GITScm polling
Pipeline:
	Definition:
		Pipeline script from SCM:
			Указываем Git, ссылку на репозиторий и ветку
		Script Path:
			Указываем путь к файлу.jtnkins в репозитории
Apply
Save
```
8. Создаём access token на dockerhub:
```
	Account settings
	Personal access tokens
	Generate new token
```

9. Создаём Credentials:
```
	Dashboard
	Manage Jenkins
	Credentials
	System
	Global credentials (unrestricted)
	Add credentials
	Настраиваем credential
	Create
```

10. Ставим плагин для того, чтобы работать с докером в пайплайнах:
```
	Dashboard
	Manage Jenkins
	Plugins
	Avalible plugins
	Устанавливаем плагин Docker Pipeline
```

11. В репозитории переходим в Settings -> Webhooks -> Add webhook:
```
Payload URL 
	http://<ip_address>:8080/github-webhook/
Which events would you like to trigger this webhook?
	переводим в режим "Send me everithing."
Update webhook
```

12. Вносим какие-либо изменения в репозиторий и наслаждаемся результатом.