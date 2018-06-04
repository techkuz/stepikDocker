Проверить корректность установки можно запустив команду:  
`$ sudo docker version`  

Документация:  
`docker help`  
`docker run --help`  
`man docker`

`docker run --rm parseq/hello-docker <message>`
(https://hub.docker.com/r/parseq/hello-docker)  
`--rm` удалить после использования  
`run` создает контейнер и выполняет операцию  
`parseq/hello-docker` - образ

**Контейнеры**  
`docker create`  
создает контейнер, но не стартует, как в `docker run`

`docker ps -a`  
список контейнеров  
`docker ps`  
список активных контейнеров

`docker rm`  
удалить контейнер

**Образы**  
`docker images`  
список образов

`docker rmi`    
удалить образ

`docker rmi $(docker images | grep '^<none>' | awk '{print $3}')`  
Или  
`docker rmi $(docker images -f "dangling=true" -q)`  
Удалить все образы, не помеченные тегами



`docker pull ubuntu:15.04`  
стянуть образ убунты

`docker commit container nameNewImage`  
создать новый образ из контейнера

`docker history image`  
История добавления слоев образа

`docker commit --change='ENTRYPOINT ["python3"]' create-image-from-me`  
создать образ из контейнера, изменив точку входа  

`docker build -t image-from-dockerfile .`  
создать образ из DockerFile (сам Dockerfile должен лежать в папке, в которой находятся только файлы, относящиеся к созданию образа) 
`-t` имя образа
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/ - Dockerfile best practices

Контейнер может быть запущен в режиме демона (ключ -d) и на переднем плане (foreground). Во втором случае можно присоединить к контейнеру терминал и взаимодействовать с ним в интерактивном режиме:  
`docker run --rm --name stepik-task -it ubuntu:14.04`  
Ключ `--name` задает имя создаваемому контейнеру, а ключ `--rm` указывает на то, что после завершения работы контейнер будет удален.  
`docker run --rm -it ubunt:14.04`  
Контейнер с убунту и терминалом


Удалить все контейнеры кроме тех, который оканчиваются на '_data':  
`docker rm $(docker ps -a | grep -v "_data" | awk 'NR>1 {print $1}')`



Для хранения персистентных данных могут использоваться директории хоста. Для того, чтобы примонтировать директорию необходимо добавить ключ `-v`, например:  
`docker run --rm -it -v /home:/home ubuntu:14.04`
Первый аргумент – откуда происходит монтирование, второй – куда. В приведенном примере директория `/home` доступна из контейнера.



**Data volumes**  
Docker никогда не удаляет data volumes, даже если контейнеры, которые их создали, удалены.

Для того чтобы посмотреть список осиротевших томов, используйте команду:

`docker volume ls -qf dangling=true`  
Для удаления таких томов:
`docker volume rm $(docker volume ls -qf dangling=true)`


**Ports**  
Если в контейнере запущены приложения, слушающие на определенных портах, можно обеспечить доступ к этим портам с хоста.

Для того чтобы пробросить порты контейнера при запуске необходимо использовать ключ `-p`:

`docker run -d --name port-export -p <port_on_host_machine>:<port_inside_container> image`  
Example: `docker run -d --name port-export -p 8085:80 parseq/stepik-ports-docker`

Для подключения к уже запущенному контейнеру удобно использовать команду:  
`docker exec -it <container-name> bash`

**Networks**  

`docker network create custom`  
`docker inspect network custom | more`  
`docker run -it --rm --name one --network=custom ubuntu:14.04`


В Dockerfile избегайте команд  `apt-get upgrade` и  `apt-get dist-upgrade`, оптимальный синтаксис команды для установки пакетов выглядит так:

```
RUN apt-get update && apt-get install -y \
    bzr \
    cvs \  
    git \
    mercurial \
    subversion
```

