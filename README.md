# Домашнее задание к занятию "`8.3 GitLab`" - `Барановский Станислав`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

## Задание 1

`Что нужно сделать:`

1. `Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в этом репозитории.`
2. `Создайте новый проект и пустой репозиторий в нём.`
3. `Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме Docker. Раннер можно регистрировать и запускать на той же виртуальной машине, на которой запущен GitLab.`

`В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.`
```
sudo echo '127.0.2.1	gitlab.localdomain gitlab' >> /etc/hosts
sudo apt install -y docker-ce docker-compose docker-doc
sudo apt install -y curl openssh-server ca-certificates tzdata perl
#Разворачиваем локально gitlab
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt install -y gitlab-ce
sudo nano /etc/gitlab/gitlab.rb  #Прописываем url  нашегосервера gitlab : external_url 'http://gitlab.localdomain'
sudo gitlab-ctl reconfigure
sudo gitlab-ctl start
sudo gitlab-ctl status
#Задаём пароль пользователю root (gitlab)
sudo gitlab-rails console
  user = User.find_by_username 'root'
  new_password = '1q2w3e4r5t'
  user.password = new_password
  user.password_confirmation = new_password
  user.save!
  exit
#Запускаем gitlab-runner
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
sudo chmod +x /usr/local/bin/gitlab-runner
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
sudo gitlab-runner register --url http://gitlab.localdomain/ --registration-token 9vtjaLAmUx_vBeYw4cAV
```
![Проект и репозиторий. Часть 1](https://github.com/StanislavBaranovskii/8-3-hw/blob/master/img/8-3-1-1.png "Проект и репозиторий. Часть 1")
![Проект и репозиторий. Часть 2](https://github.com/StanislavBaranovskii/8-3-hw/blob/master/img/8-3-1-1.png "Проект и репозиторий. Часть 2")
![Настройки runner](https://github.com/StanislavBaranovskii/8-3-hw/blob/master/img/8-3-1-3.png "Настройки раннера")

---

## Задание 2

`Что нужно сделать:`

1. `Запушьте репозиторий на GitLab, изменив origin. Это изучалось на занятии по Git.`
2. `Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.`

`В качестве ответа в шаблон с решением добавьте:`
* `файл gitlab-ci.yml для своего проекта или вставьте код в соответствующее поле в шаблоне;`
* `скриншоты с успешно собранными сборками.`

```
git remote rename origin old-origin
git remote add origin http://gitlab.localdomain/gitlab-instance-65255979/gitlab-my-local.git
git push -u origin --all
git push -u origin --tags
```
Листинг файла gitlab-ci.yml
```
stages:
 - test
 - build
test:
 stage: test
 image: golang:latest
 script:
 - go test .
build_manual:
 stage: build
 except:
 - master
 image: docker:latest
 script:
 - docker build .
build:
 stage: build
 only:
 - master
 image: docker:latest
 script:
 - docker build .
```

![Настройки runner](https://github.com/StanislavBaranovskii/8-3-hw/blob/master/img/8-3-2-1.png "Настройки раннера")

---