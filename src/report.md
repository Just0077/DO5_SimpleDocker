## Part 1. Готовый докер  
#### 1) Взять официальный докер образ с nginx и выкачать его при помощи docker pull  
* используем команду `sudo docker pull nginx`  
![image.1.0.jpg](./image/1.0.jpg)

#### 2) Проверить наличие докер образа через docker images  
* используем команду `sudo docker images`  
![image.1.1.jpg](./image/1.1.jpg)

#### 3-4) Запустить докер образ через `docker run -d [image_id|repository]` и проверить что образ запустился через `docker ps`  
* используем команду `sudo docker run -d nginx` и проверяем, что образ запустился с помощью команды `sudo docker ps`  
![image.1.2.jpg](./image/1.2.jpg)

#### 5) Посмотреть информацию о контейнере через `docker inspect [container_id|container_name]`  
* используем команду `sudo docker inspect container_id`  
![image.1.3.jpg](./image/1.3.jpg)

#### 6) По выводу команды определить и поместить в отчёт размер контейнера, список замапленных портов и ip контейнера  
* найдём размер контейнера командой `sudo docker inspect container_id --size | grep -i SizeRw`  
![image.1.4.jpg](./image/1.4.jpg)


* найдём в выводе команды `sudo docker inspect container_id` список портов  
![image.1.5.jpg](./image/1.5.jpg)


* найдём ip контейнера командой `sudo docker inspect container_id --size | grep -i ip`  
![image.1.6.jpg](./image/1.6.jpg)

#### 7-8) Остановить докер образ через `docker stop [container_id|container_name]` и проверить, что образ остановился через `docker ps`  
* используем команду `sudo docker stop container_id` и проверяем остановку командой `sudo docker ps`  
![image.1.7.jpg](./image/1.7.jpg)

#### 9) Запустим докер с замапленными портами 80 и 443 на локальную машину через команду `run`  
* используем команду `sudo docker run -d -p 8-:80 -p 443:443 nginx` и сразу проверим запуск и порты командой `sudo docker ps`  
![image.1.8.jpg](./image/1.8.jpg)

#### 10) Проверим, что в браузере по адресу localhost:80 доступна стартовая страница nginx  
* Открываем любой браузер и в адресной строке пишем localhost:80  
![image.1.9.jpg](./image/1.9.jpg)

#### 11) Перезапустим докер контейнер через `docker restart [container_id|container_name]`
![image.1.10.jpg](./image/1.10.jpg)


## Part 2. Операции с контейнером  
#### 1) Прочитать конфигурационный файл nginx.conf внутри докер контейнера через команду `exec`  
* используем команду `sudo docker exec container_id cat /etc/nginx/nginx.conf`  
![image.2.0.jpg](./image/2.0.jpg)

#### 2) Создать на локальной машине файл nginx.conf  
* создаём файл  
![image.2.1.jpg](./image/2.1.jpg)

#### 3) Настроить в нем по пути /status отдачу страницы статуса сервера nginx  
* дописываем блок http  
![image.2.2.jpg](./image/2.2.jpg)


также потребовалось закомментировать `include /etc/nginx/conf.d/*.conf`, потому что с этой строкой не отображалась страница status.  

#### 4-5) Скопировать созданный файл nginx.conf внутрь докер образа через команду `docker cp` и Перезапустить nginx внутри докер образа через команду `exec`  
* копируем файл командой `sudo docker cp nginx.conf container_id:etc/nginx/` и перезапускаем nginx командой `sudo docker exec container_id nginx -s reload`  

![image.2.3.jpg](./image/2.3.JPG)

![image.2.4.jpg](./image/2.4.JPG)

#### 6) Проверить, что по адресу localhost:80/status отдается страничка со статусом сервера nginx  
* открываем браузер и проверяем  

![image.2.5.jpg](./image/2.5.JPG)

#### 7) Экспортировать контейнер в файл container.tar через команду export  
* экспортируем контейнер командой `sudo docker export -o container.tar container_id  `
![image.2.6.jpg](./image/2.6.JPG)

#### 8) Остановить контейнер  
* останавливаем контейнер командой `sudo docker stop container_id`  
![image.2.7.jpg](./image/2.7.JPG)

#### 9-10) Удалить образ через `docker rmi [image_id|repository]`, не удаляя перед этим контейнеры  
* удаляем образ командой `sudo docker rmi -f nginx`  

![image.2.8.jpg](./image/2.8.JPG)

* удаляем контейнер командой `sudo docker rm container_id` 

![image.2.9.jpg](./image/2.9.JPG)

#### 11) Импортировать контейнер обратно через команду import  
* используем команду `sudo docker import -c 'CMD ["nginx", "-g", "daemon off;"]' container.tar part_2`  

![image.2.10.jpg](./image/2.10.JPG)

#### 12) Запустить импортированный контейнер  
* запускаем контейнер командой `sudo docker run -d -p 80:80 -p 443:443 part_2`  
![image.2.11.jpg](./image/2.11.JPG)

#### 13) Проверить, что по адресу localhost:80/status отдается страничка со статусом сервера nginx  
* открываем браузер и смотрим  
![image.2.12.jpg](./image/2.12.JPG)

## Part 3. Мини веб-сервер  
* Пишем мини сервер на C и FastCgi, который будет возвращать простейшую страничку с надписью `Hello World!`  
![image.3.0.jpg](./image/3.0.JPG)


* Пишем свой nginx.conf, который будет проксировать все запросы с 81 порта на 127.0.0.1:8080  
![image.3.1.jpg](./image/3.1.JPG)


* Качаем образ nginx, запускаем контейнер, копируем c файл сервера и conf файл nginx  
![image.3.2.jpg](./image/3.2.JPG)


* Заходим в контейнер командой `docker exec -it container_id bash`, обновляем репозитории, устанавливаем gcc, spawn-fcgi и libfcgi-dev  
![image.3.3.jpg](./image/3.3.JPG)


* Компилируем и запускаем сервер  
![image.3.4.jpg](./image/3.4.JPG)

![image.3.5.jpg](./image/3.5.JPG)


* проверяем нашу страничку  
![image.3.6.jpg](./image/3.6.JPG)

## Part 4. Свой докер  
* Создаём докерфайл  
![image.4.1.jpg](./image/4.1.JPG)

* Создаём скрипт, выполняющий роль entrypoint  
![image.4.2.jpg](./image/4.2.JPG)


* Собираем образ через `docker build` при этом указав имя и тег  
![image.4.4.jpg](./image/4.4.JPG)


* проверяем через `docker images`, что все собралось корректно  
![image.4.5.jpg](./image/4.5.JPG)

* Запускаем собранный докер образ с маппингом 81 порта на 80 на локальной машине и маппингом папки ./nginx внутрь контейнера по адресу, где лежат конфигурационные файлы nginx'а  
* проверяем в браузере  
![image.4.7.jpg](./image/4.7.JPG)

* Дописываем в ./nginx/nginx.conf проксирование странички /status, по которой надо отдавать статус сервера nginx  
![image.4.8.jpg](./image/4.8.JPG)


* Перезапускаем докер образ, проверяем, заглядываем в браузер  
![image.4.9.jpg](./image/4.9.JPG)
![image.4.10.jpg](./image/4.10.JPG)

## Part 5. Dockle  
* сперва установим докл  
![image.5.1.jpg](./image/5.1.JPG)

* потом проверим образ  
![image.5.2.jpg](./image/5.2.JPG)

* пересобираем образ  
![image.5.3.jpg](./image/5.3.JPG)

* C ошибкой CIS-DI-0010 можно было разобраться только сменой образа на Alpine  
* переписываем докерфайл и проверяем командой
![image.5.4.jpg](./image/5.4.JPG)

## Part 6. Базовый Docker Compose  
* Перепишем скрипт entrypoint для второго контейнера, иначе он будет завершать работу после `docker-compose up`  
![image.6.1.jpg](./image/6.1.JPG)


* Перепишем _**nginx.conf**_ для проксирования  
![image.6.2.jpg](./image/6.2.JPG)


* напишем **_docker-compose.yml_**  
![image.6.3.jpg](./image/6.3.JPG)


* билдим командой `sudo docker-compose build` 
![image.6.4.jpg](./image/6.4.JPG)


* запускаем командой `sudo docker-compose up`
![image.6.5.jpg](./image/6.5.JPG)


* проверяем в браузере

![image.6.6.jpg](./image/6.6.JPG)

![image.6.7.jpg](./image/6.7.JPG)
