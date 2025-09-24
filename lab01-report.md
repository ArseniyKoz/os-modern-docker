# Лабораторная №1

Покрываемые навыки: запуск контейнеров, теги образов, detached/foreground, публикация портов, тома (bind‑mount), exec, attach, логи, жизненный цикл, inspect, аккуратная очистка.

## 1) Версия и теги
`docker version`: Client: 28.0.4, Server.Engine: 28.0.4\
<img width="747" height="652" alt="image" src="https://github.com/user-attachments/assets/c62ebb7b-379e-40a0-8293-5660f67dbec5" />

`nginx:1.29.1-alpine` Тэг - `1.29.1-alpine`  

## 2) Первый запуск сервиса (detached)  
Запускаем контейнер `docker run -d --name lab-web-arseniy -p 8080:80 nginx:1.29.1-alpine` :  
<img width="1870" height="590" alt="image" src="https://github.com/user-attachments/assets/74be72fd-5adf-4741-825a-3ae15ac4b414" />

`docker stop lab-web-arseniy` - останавливаем  

`docker rm lab-web-arseniy`  - удаляем  

`docker run -d --name lab-web-arseniy -p 1905:80 nginx:1.29.1-alpine` , порт - 1905:80  - запуск на своем порте  

<img width="1851" height="335" alt="image" src="https://github.com/user-attachments/assets/ad1c3e66-f87f-4da0-9f02-cffa34ebcae9" />

`docker ps`  - список всем контейнером  
<img width="1591" height="53" alt="image" src="https://github.com/user-attachments/assets/3aed3cf6-65fe-438a-ac34-3e4ef7157e70" />


## 3) Том (bind‑mount): сайт/данные из папки хоста  
`mkdir site`  - создаем папку `site`  

`echo "<h1>Was</h1>" > site/index.html`  - создание html файла  

`docker run -d --name lab-web-arseniy -p 1905:80 -v "${PWD}\site:/usr/share/nginx/html:ro" nginx:1.29.1-alpine `  - Запуск контейнера с подключенным томом, с доступом только для чтения  

<img width="924" height="362" alt="image" src="https://github.com/user-attachments/assets/0625576f-536e-4853-a67c-45cff00fec54" />

`echo "<h1>Now</h1>" > site/index.html`  - Изменение содержимого html файла 

<img width="735" height="387" alt="image" src="https://github.com/user-attachments/assets/d0a835d9-e79f-4b1f-96e3-9be3075c7b68" />


В отличии от контейнера, том — это постоянное хранилище. Это папка вне контейнера (на хосте) или область, которая упраляется Docker-ом, куда контейнер складывает данные. Даже если контейнер удалить и создать новый — данные в томе сохранятся.

## 4) Интерактив / exec

`docker exec -it lab-web-arseniy /bin/sh`  - подключение к активному образу  

<img width="595" height="143" alt="image" src="https://github.com/user-attachments/assets/dc0afe5e-b499-4055-8868-f013e5bb645a" />

<img width="399" height="70" alt="image" src="https://github.com/user-attachments/assets/f496c1a5-d27a-4164-9b6a-9f4d6fbcaa60" />

`Ctrl + z ` - выйти из интерактивного режима.    

Запуск без read-only:  
`docker run -d --name lab-web-arseniy -p 1905:80 -v "${PWD}\site:/usr/share/nginx/html" nginx:1.29.1-alpine`  

`docker exec -it lab-web-arseniy /bin/sh`  

<img width="407" height="98" alt="image" src="https://github.com/user-attachments/assets/966cf14a-afce-47c6-95ba-e3db00205577" />

Файл на хосте:  
<img width="761" height="274" alt="image" src="https://github.com/user-attachments/assets/c8a83c0d-85c1-491f-97ed-9a5fdda0e19e" />

## 5) Логи и attach
Несколько раз обновил хост + два раза попробовал перейти на /nope  
`docker logs lab-web-arseniy`, получившиеся логи:  
<img width="1134" height="368" alt="image" src="https://github.com/user-attachments/assets/c13e7517-a701-46bc-9a71-1b312bde14a0" />
`docker attach lab-web-arseniy`  
<img width="1142" height="129" alt="image" src="https://github.com/user-attachments/assets/a850d006-cda9-42c9-968d-e7d86c36df9f" />
Для выхода из attach, не останавливая контейнер, нажимаем Ctrl + P, затем Ctrl + Q.  
!!! Это можно сделать, только если контейнер запущен в интерактивном режиме.  

## 6) Краткоживущий процесс
`docker run ubuntu echo "Sleep"`  - запуск контейнера с заданной командой - вывести в консоль `Sleep`  
<img width="1304" height="154" alt="image" src="https://github.com/user-attachments/assets/fd52f3d6-6ce7-4a9d-87d9-8f383824297e" />
Контейнер, после выполнения задач, сразу переходит в состояние Exited, потому что у него закончились задачи.  

## 7) Inspect
`docker inspect lab-web-arseniy`  - вывод данных про контейнер   

Mount `docker inspect lab-web-arseniy --format='{{json .Mounts}}' | python -m json.tool` :  

`    {  
        "Type": "bind",  
        "Source": "C:\\Users\\Master\\site",  
        "Destination": "/usr/share/nginx/html",  
        "Mode": "",  
        "RW": true,   
        "Propagation": "rprivate"  
    }`  

Ports `docker inspect lab-web-arseniy --format='{{json .NetworkSettings.Ports}}' | python -m json.tool `:    
`{  
    "80/tcp": [  
        {  
            "HostIp": "0.0.0.0",  
            "HostPort": "1905"  
        }  
    ]  
}`  

## 8) Чистка

Сначала останавливаем все запущенные контейнеры, затем удаляем.  
`docker stop lab-web-arseniy  
 docker rm lab-web-arseniy  
 docker rm 63b8223ef0a5   
`
Сначала узнаем какие образы скачаны:  
<img width="801" height="246" alt="image" src="https://github.com/user-attachments/assets/71dc4b1f-580c-49d6-9379-100eedfd9222" />

Для удаления образа воспользуемся следующей командой:   
`docker rmi nginx:1.29.1-alpine`    
Получаем итоговый список:  
<img width="785" height="219" alt="image" src="https://github.com/user-attachments/assets/7d262617-9088-442b-8d44-c084e3e5ba2c" />


## Мини‑квиз (в отчёте, без запуска команд) 
### Что произойдёт с данными, созданными внутри контейнера, если удалить контейнер без тома?
Данные внутри контейнера будут удалены вместе с контейнером, так как контейнер — одноразовый. У контейнера есть своя временная файловая система. Когда контейнер удаляют — все файлы внутри него пропадают.

### Чем отличается порт хоста от порта в контейнере?
Порт хоста доступен всей системе и запускается на физической машине, а порт контейнера запускается только в изолированной среде и доступен только внутри контейнера.

### Для чего нужна пара флагов интерактивного запуска, и когда одного из них достаточно?
По умолчанию контейнер не слушает ввод с клавиатуры. Чтобы взаимодействовать с ним напрямую, используйте флаги -i (интерактивный режим) и -t (псевдотерминал).
Если же нам нужно только возможность чтения/ввода данных, например:
`docker run -i python:alpine python <<< "print(2+2)"`,
то можно обойтись одним флагом. 

### Что показывает Mounts в inspect и как понять, что это именно bind‑mount?
Mounts показывает тома, которые подключены к контейнеру. Чтобы понять, что это именно bind-mount, можно посмотреть на type в json отображении. Если там указан тип `bind`, то это bind‑mount.

### Почему образ может не удаляться, и что нужно сделать перед удалением?
Образ может не удаляться, если он используется хотя бы одни контейнером или является родительским для другого образа. Соответсвтенно, чтобы удалить такой образ, нужно так же удалить все зависимые от него части.
