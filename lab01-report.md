# Лабораторная №1
## 1) Версия и теги
`docker version`: Client: 28.0.4, Server.Engine: 28.0.4
<img width="747" height="652" alt="image" src="https://github.com/user-attachments/assets/c62ebb7b-379e-40a0-8293-5660f67dbec5" />

`nginx:1.29.1-alpine` Тэг - `1.29.1-alpine`

## 2) Первый запуск сервиса (detached)
`docker run -d --name lab-web-arseniy -p 8080:80 nginx:1.29.1-alpine` : 
<img width="1870" height="590" alt="image" src="https://github.com/user-attachments/assets/74be72fd-5adf-4741-825a-3ae15ac4b414" />

`docker stop lab-web-arseniy`,

`docker rm lab-web-arseniy`,

`docker run -d --name lab-web-arseniy -p 1905:80 nginx:1.29.1-alpine` , порт - 1905:80

<img width="1851" height="335" alt="image" src="https://github.com/user-attachments/assets/ad1c3e66-f87f-4da0-9f02-cffa34ebcae9" />

`docker ps`
<img width="1591" height="53" alt="image" src="https://github.com/user-attachments/assets/3aed3cf6-65fe-438a-ac34-3e4ef7157e70" />


## 3) Том (bind‑mount): сайт/данные из папки хоста
`mkdir site`

`echo "<h1>Было</h1>" > site/index.html`
