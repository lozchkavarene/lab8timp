# Отчет по лабораторной работе № 8

### Цель работы

Данная лабораторная работа посвещена изучению систем автоматизации развёртывания и управления приложениями на примере **Docker**

### Задания

- [x] 1. Создать публичный репозиторий с названием **lab8timp** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

### Выполненные команды 

Все файлы, полученные в ходе выполнения лабораторной работы см. в текущем репозитории

#### Настройка Docker:

Создание образа ubuntu:

```sh
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```
Обновление системы и установка компиляторов и средств сборки:

```sh
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

Копирование текущей директории в докер:

```sh
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```
Компиляция проекта:

```sh
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

Переменная среды LOG_PATH:

```sh
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

Папка, которая будет использована для доставания файлов из контейнера

```sh
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```

Переходим в папку со скомпилированными файлами

```sh
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```

Запускаем исполняемый файл:

```sh
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

#### Работа с Docker:

```sh
$ sudo docker build -t logger .
	Sending build context to Docker daemon  23.91MB
	Step 1/8 : FROM ubuntu:18.04
	18.04: Pulling from library/ubuntu
	01bf7da0a88c: Pull complete 
    ...
    Successfully built cdff0bdafdd7
    Successfully tagged logger:latest
```

```sh
$ docker images
	REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
	logger       latest    29c4724ee8c3   About a minute ago   347MB
	ubuntu       18.04     4eb8f7c43909   12 days ago          63.1MB
```

```sh
$ mkdir logs
$ docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>
```

```sh
$ docker inspect logger >> inspect_logger.txt
```

```sh
$ cat logs/log.txt
	text1
	text2
	text3
```

```sh
$ vim .travis.yml

services:
- docker<ESC>
script:
- docker build -t logger .<ESC>
```

```sh
$ git add Dockerfile
$ git add .travis.yml
$ git commit -m"adding Dockerfile"

$ travis login --auto
$ travis enable
```

```sh
$ git push origin master
```

## Links

- [Book](https://www.dockerbook.com)
- [Instructions](https://docs.docker.com/engine/reference/builder/)
