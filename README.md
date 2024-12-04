# Установка и настройка:

1. Создаём 2 виртуальные машины (я создал две ubuntu 24.04 в AWS). Машины дальше я буду именовать как "server" и "host". Так как я использую AWS, то пользователями будут ubuntu. Также не забываем открыть нужные нам порты, как минимум 8080;

2. На server ставим сервер Jenkins (https://www.jenkins.io/doc/book/installing/linux/). Желательно начать с пункта про Java (он указан ниже), а потом уже к установке Jenkins;

3. На host ставим docker (https://docs.docker.com/engine/install/ubuntu/). Потом добавляем пользователя ubuntu в группу docker:
	sudo usermod -aG docker ubuntu
	newgrp docker

4. На server ставим ansible (https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu);

5. На server ставим docker:
	1. pip install docker (Появится подсказка про команду 2. Потом стоит снова запустить команду 1, но linux может ругаться из-за того что пакет Python не входит в состав Debian. ПОэтому после 2 мне пришлось выполнить 3, а не 1. В итоге всё равно всё работает)
	2. sudo apt install python3-pip
	3. sudo apt install python3-docker

6. На server в /etc/ansible/hosts прописываем группу хостов для ansible. Создаём директорию /var/lib/jenkins/playbooks для плейбуков. Добавляем ansible-playbook в директорию /var/lib/jenkins/playbooks;

7. Переходим на веб-сервер jenkins (<ip_address:8080>), создаём Freestyle project:
	Source Code Management
		переводим в режим "Git". Потом указываем ссылку на репозиторий и меняем ветку на main
	Build Triggers
		ставим галочку на GitHub hook trigger for GITScm polling
	Build Steps:
		создаём новую консоль shell и в ней прописываем запуск ansible-playbook, который хранится в /var/lib/jenkins/playbooks
	Apply
	Save

8. В репозитории переходим в Settings -> Webhooks -> Add webhook
	Payload URL 
		http://<ip_address>:8080/github-webhook/
	Which events would you like to trigger this webhook?
		переводим в режим "Send me everithing."
	Update webhook

9. Вносим какие-либо изменения в репозиторий и наслаждаемся результатом.