# devops-pw11-CI
DevOps Project work 11 - CI/CD, Nginx, Docker, Index.html, Md5

# Что нужно сделать

1. Создать виртуальную машину с характеристиками: 2vCPU, 2GB, RAM, 20GB, HDD.

2. Поднять на ней CI-сервер Jenkins.

3. Создать репозиторий Github с файлом index.html.

4. Настроить CI:

* Запускающий контейнер с nginx (версия на ваше усмотрение) с пробросом порта 80 в порт 9889. При обращении к nginx в контейнере по HTTP, nginx должен выдавать измененный файл index.html.
* Проверяющий код ответа запущенного контейнера при HTTP-запросе (код должен быть 200).
* Сравнивающий md5-сумму измененного файла с md5-суммой файла, отдаваемого nginx при HTTP-запросе (суммы должны совпадать).
* Триггер для старта CI: внесение изменений в созданный вами файл index.html из п.3. В случае выявления ошибки (в двух предыдущих пунктах), должно отправляться оповещение вам в удобный канал связи — Telegram/Slack/email. Текст оповещения — на ваше усмотрение.
* После выполнения CI созданный контейнер должен удаляется.

5. Прислать написанный для CI код (или скрин с описанием джоба), ссылку на репозиторий и на развернутую CI-систему.

# Установка

1. Git
```console
sudo apt-get install git
```

2. Docker
```console
 sudo apt-get update
 sudo apt-get install ca-certificates curl gnupg lsb-release
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
3. Docker-Compose
```console
sudo curl -k -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo docker-compose --version
```
4. Jenkins LTS (Long-Term Support) release - Jenkins 2.332.3 on 2022.05.05
```console
sudo apt-get install openjdk-11-jdk

java -version
> openjdk version "11.0.15" 2022-04-19
> OpenJDK Runtime Environment (build 11.0.15+10-Ubuntu-0ubuntu0.20.04.1)
> OpenJDK 64-Bit Server VM (build 11.0.15+10-Ubuntu-0ubuntu0.20.04.1, mixed mode, sharing)

wget -q -O - http://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
sudo ufw allow 8080

sudo usermod -aG docker jenkins
chmod 777 /var/run/docker.sock

id jenkins
> uid=109(jenkins) gid=115(jenkins) groups=115(jenkins),998(docker)

systemctl status jenkins
http://{Public IP}:8080
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

5. Create Telegram Bot

Additional information:

[Telegram Bot API](https://core.telegram.org/bots/api)

[Bots: An introduction for developers](https://core.telegram.org/bots#6-botfather)

6. Create Pipeline

Set Parameterized Builds for Telegram Bot send message
```
1) TOKEN = 110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw
2) CHAT_ID = 123
```