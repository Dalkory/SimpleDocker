# Simple Docker

Введение в докер. Разработка простого докер образа для собственного сервера.

1. [Готовый докер](#part-1-готовый-докер)
2. [Операции с контейнером](#part-2-операции-с-контейнером)
3. [Мини веб-сервер](#part-3-мини-веб-сервер)
4. [Свой докер](#part-4-свой-докер)
5. [Dockle](#part-5-dockle)
6. [Базовый Docker Compose](#part-6-базовый-docker-compose)


## Part 1. Готовый докер

- `apt install docker.io`

##### Взять официальный докер образ с **nginx** и выкачать его при помощи `docker pull`

- `sudo docker pull nginx`

![install](image.png)

##### Проверить наличие докер образа через `docker images`

- `sudo docker images`

![images](image-1.png)

##### Запустить докер образ через `docker run -d [image_id|repository]` и проверить, что образ запустился через `docker ps`

- `sudo docker run -d [image_id|repository]` 

- `sudo docker ps`

![Alt text](image-2.png)

##### Посмотреть информацию о контейнере через `docker inspect [container_id|container_name]`

- `sudo docker inspect [container_id|container_name]`

![Alt text](image-3.png)

##### По выводу команды определить и поместить в отчёт размер контейнера, список замапленных портов и ip контейнера

- размер контейнера `sudo docker inspect [container_id|container_name] | grep -i ShmSize`

![размер](image-4.png)

- список замапленных портов `sudo docker inspect [container_id|container_name] | grep -i -A 2 dPorts`

![порты](image-5.png)

- ip `sudo docker inspect [container_id|container_name] | grep -i ip`

![ip](image-6.png)

##### Остановить докер образ через `docker stop [container_id|container_name]`

- `sudo docker stop [container_id|container_name]`

![stop](image-7.png)

##### Проверить, что образ остановился через `docker ps`

![ps](image-8.png)

##### Запустить докер с портами 80 и 443 в контейнере, замапленными на такие же порты на локальной машине, через команду *run*

- `sudo docker run -d -p 80:80 -p 443:443 nginx`

![port](image-9.png)

##### Проверить, что в браузере по адресу *localhost:80* доступна стартовая страница **nginx**

![ping](image-10.png)

- у меня ubuntu lts, браузера не будет, но по пингу понято, что конект есть.

![lts](image-13.png)

##### Перезапустить докер контейнер через `docker restart [container_id|container_name]`

- `sudo docker restart [container_id|container_name]`

![restart](image-11.png)

##### Проверить любым способом, что контейнер запустился

![done](image-12.png)

## Part 2. Операции с контейнером

##### Прочитать конфигурационный файл *nginx.conf* внутри докер контейнера через команду *exec*

- `sudo docker exec [container_id|container_name] cat /etc/nginx/nginx.conf`

![Alt text](image-14.png)

##### Создать на локальной машине файл *nginx.conf*

- `touch nginx.conf `

![Alt text](image-15.png)

##### Настроить в нем по пути */status* отдачу страницы статуса сервера **nginx**

![Alt text](image-16.png)

- За основу я беру файл **nginx.conf** из контейнера.

##### Скопировать созданный файл *nginx.conf* внутрь докер образа через команду `docker cp`

- `sudo docker cp nginx.conf 682b5a4098d4:/etc/nginx/`

##### Перезапустить **nginx** внутри докер образа через команду *exec*

- `sudo docker exec 682b5a4098d4 nginx -s reload`

##### Проверить, что по адресу *localhost:80/status* отдается страничка со статусом сервера **nginx**

- `curl localhost:80/status`

- общий скрин, 4 заданя.

![Alt text](image-18.png)

##### Экспортировать контейнер в файл *container.tar* через команду *export*

- `sudo docker export 682b5a4098d4 > container.tar`

##### Остановить контейнер

- `sudo docker stop 682b5a4098d4`

- общий скрин, 2 заданя.

![Alt text](image-19.png)

##### Удалить образ через `docker rmi [image_id|repository]`, не удаляя перед этим контейнеры

![del](image-20.png)

##### Удалить остановленный контейнер

![Alt text](image-21.png)

##### Импортировать контейнер обратно через команду *import*

- ["nginx", "-g", "daemon off;"] гарантирует, что Nginx останется «на переднем плане», так что Docker сможет правильно отслеживать процесс (в противном случае контейнер остановится сразу после запуска)

![Alt text](image-22.png)

##### Запустить импортированный 

- `sudo docker run -d -p 80:80 -p 443:443 f30c3d1adb02`

##### Проверить, что по адресу *localhost:80/status* отдается страничка со статусом сервера **nginx**

- `curl localhost:80/status`

![Alt text](image-23.png)

## Part 3. Мини веб-сервер

##### Написать мини сервер на **C** и **FastCgi**, который будет возвращать простейшую страничку с надписью `Hello World!`

![си](image-24.png)

##### Написать свой *nginx.conf*, который будет проксировать все запросы с 81 порта на *127.0.0.1:8080*

![сonf](image-25.png)

##### Запустить написанный мини сервер через *spawn-fcgi* на порту 8080

- cоздаем контейнер с портом 81

![aa](image-26.png)

- копирую файлы в контейнер

![сopy](image-27.png)

- `sudo docker exec -it nginx1 bash` - зашел в контейнер, а после скачал все необходимое

![install](image-28.png)


##### Проверить, что в браузере по *localhost:81* отдается написанная вами страничка

- компилируем, запускаемся, обновляемся и проверяем

![Hello World](image-29.png)

##### Положить файл *nginx.conf* по пути *./nginx/nginx.conf* (это понадобится позже)

## Part 4. Свой докер

#### Написать свой докер образ, который:
##### 1) собирает исходники мини сервера на FastCgi из [Части 3](#part-3-мини-веб-сервер)
##### 2) запускает его на 8080 порту
##### 3) копирует внутрь образа написанный *./nginx/nginx.conf*
##### 4) запускает **nginx**.

- Сначала пишем start.sh, в котором записываем команды для ENTRYPOINT. Entrypoint в Dockerfile определяет команды, которые будут запущены при запуске контейнера из образа.

![start](image-30.png)

![doc](image-32.png)

##### Собрать написанный докер образ через `docker build` при этом указав имя и тег

![build](image-33.png)

##### Проверить через `docker images`, что все собралось корректно

![imag](image-34.png)

##### Запустить собранный докер образ с маппингом 81 порта на 80 на локальной машине и маппингом папки *./nginx* внутрь контейнера по адресу, где лежат конфигурационные файлы **nginx**'а (см. [Часть 2](#part-2-операции-с-контейнером))

![run](image-35.png)

![nginx](image-37.png)

##### Проверить, что по localhost:80 доступна страничка написанного мини сервера

![curl](image-36.png)

##### Дописать в *./nginx/nginx.conf* проксирование странички */status*, по которой надо отдавать статус сервера **nginx**

![status](image-38.png)

##### Перезапустить докер образ
##### Проверить, что теперь по *localhost:80/status* отдается страничка со статусом **nginx**

![done](image-39.png)

## Part 5. **Dockle**

- Скачиваем Dockle

![install](image-40.png)

##### Просканировать образ из предыдущего задания через `dockle [image_id|repository]`

![chek](image-41.png)

##### Исправить образ так, чтобы при проверке через **dockle** не было ошибок и предупреждений

- В Dockerfile был изменен USER с root на nginx
- Был добавлен HEALTHCHECK для проверок образа
- Изменены разрешения для файлов в соответствии с требованием Dockle
- Dockle не понравились ключи nginx, но нам они нужны, поэтому при вызове Dockle-проверки мы сделаем для них исключения
- `export DOCKER_CONTENT_TRUST=1` 
- C ошибкой CIS-DI-0010 можно было разобраться только сменой образа на `Alpine`

![doc](image-42.png)

- собираем исправленный 

![sbor](image-43.png)

- проверяем

![dockle](image-45.png)

## Part 6. Базовый **Docker Compose**

##### Написать файл *docker-compose.yml*, с помощью которого:
##### 1) Поднять докер контейнер из [Части 5](#part-5-инструмент-dockle) _(он должен работать в локальной сети, т.е. не нужно использовать инструкцию **EXPOSE** и мапить порты на локальную машину)_
##### 2) Поднять докер контейнер с **nginx**, который будет проксировать все запросы с 8080 порта на 81 порт первого контейнера
##### Замапить 8080 порт второго контейнера на 80 порт локальной машины

![yml](image-46.png)

- Также создаем необходимые файлы чтобы поднять nginx:

![aaaaaaaaaaaa](image-47.png)

![b](image-48.png)

##### Остановить все запущенные контейнеры

- `docker system prune -a --volumes`

![del](image-50.png)

##### Собрать и запустить проект с помощью команд `docker-compose build` и `docker-compose up`

![bild](image-52.png)

![up](image-53.png)

##### Проверить, что в браузере по *localhost:80* отдается написанная вами страничка, как и ранее

![80](image-55.png)

![status](image-54.png)