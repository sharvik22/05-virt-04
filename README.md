# Домашнее задание к занятию 5. «Практическое применение Docker» Шарапат Виктор

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**


## Решение 0

![image](https://github.com/user-attachments/assets/46cfe7f3-653c-4bea-beae-841d3aae47b8)

![image](https://github.com/user-attachments/assets/9d12a2bc-2141-45c6-99ec-f1a66df1d8e0)

---

## Задача 1
1. Сделайте в своем github пространстве fork репозитория ```https://github.com/netology-code/shvirtd-example-python/blob/main/README.md```.   
2. Создайте файл с именем ```Dockerfile.python``` для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ ```python:3.9-slim```. 
Обязательно используйте конструкцию ```COPY . .``` в Dockerfile. Не забудьте исключить ненужные в имадже файлы с помощью dockerignore. Протестируйте корректность сборки.  
3. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker в venv. (Mysql БД можно запустить в docker run).
4. (Необязательная часть, *) По образцу предоставленного python кода внесите в него исправление для управления названием используемой таблицы через ENV переменную.
---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---

## Решение 1

1) Сделал fork репозитория ```https://github.com/netology-code/shvirtd-example-python/blob/main/README.md```
   
Для последующих действий с репозиторием использовал команды:


git config --global user.email "sharvik22@mail.ru"

git config --global user.name "VIKTOR"

git config --list

git clone https://github.com/sharvik22/shvirtd-example-python.git && cd shvirtd-example-python

в дальнейшем 

git add .

и git commit -m '******'

git push https://ghp_ЧЧЧЧЧЧЧЧЧЧТТТТТТТТТТТОООООООООООООgithub.com/sharvik22/shvirtd-example-python.git

2) Создайте файл с именем ```Dockerfile.python```:

   
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt ./

RUN pip install -r requirements.txt

COPY main.py ./

CMD ["python", "main.py"]


P.S. Важный момент возникает ошибка на сегоднейний день


![image](https://github.com/user-attachments/assets/cce099b3-ac7d-4654-91b4-c0635096927f)


У меня возникла ошибка связана с доступом для загрузки зависимостям pip install, но не со стороны хостовой машины, а именно docker, хотя всё скачивается и ставить без проблем. 
Решилось это бонально, после 2 часов поиска и различный проб вариантов.

На основе Dockerfile.python был собран образ, и запущен контейнекер для проверки

docker build -t python -f Dockerfile.python .

![image](https://github.com/user-attachments/assets/7999c1a8-f9b7-4b3b-9721-d81a3502911e)

---

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

2. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете ```NULL``` --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;```.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.

## Решение 3

запуск docker compose up -d 

![image](https://github.com/user-attachments/assets/6baba600-7374-4ffa-96d0-0de93b7155a4)

Подключение в db

![image](https://github.com/user-attachments/assets/7bd35044-ad6c-4875-8642-c007d0e320e8)

Запросы

![image](https://github.com/user-attachments/assets/7d6697f8-f619-47a5-a37e-4aaab4c6c6ae)

команды sql запросов:

show databases;

use virtd; 

show tables;

SELECT * from requests LIMIT 10;


---

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. ПРИМЕЧАНИЕ: Приложение весьма вероятно упадет под нагрузкой, но успеет обработать часть запросов - этого достаточно.
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. В качестве ответа повторите  sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.


## Решение 4

С помпощью terraform создал ВМ Yandex Cloud

![image](https://github.com/user-attachments/assets/87dfb03f-60a0-4972-b149-3a58681b1aa7)

создал скрипт и подключился по ssh к ВМ

![image](https://github.com/user-attachments/assets/4e1ca1dc-396d-4c0c-9178-52d3b6806447)

выполнение скрипта 

![image](https://github.com/user-attachments/assets/062af89b-1b55-4dd5-a057-13a97a8d849a)

в связи с проблемами сети не смог выловить 

![image](https://github.com/user-attachments/assets/0ae9108d-3d42-46db-8507-7811f6b90c82)

sql запросы:

![image](https://github.com/user-attachments/assets/98682ecc-f4d0-4a9d-89d4-23d7b8c27000)

---

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

## Решение 6

docker pull hashicorp/terraform:latest

![image](https://github.com/user-attachments/assets/9e052f96-7d8e-400f-95d2-9a932c70317c)

docker save -o terraform.tar hashicorp/terraform:latest

![image](https://github.com/user-attachments/assets/c4ce4bf9-7ff1-4ef8-ae53-4042b2565835)


проанализировал слои образа

dive hashicorp/terraform:latest

![image](https://github.com/user-attachments/assets/6646528a-2815-4019-a634-a7bda1cebae3)


tar -xvf terraform.tar bf3e32717f01f768e465ab01d1e0ae9e4954ef3fe9b23180b264309aa9c61257/layer.tar

tar -xvf bf3e32717f01f768e465ab01d1e0ae9e4954ef3fe9b23180b264309aa9c61257/layer.tar bin/terraform

---

## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

## Решение 6.1

docker pull hashicorp/terraform:latest

docker cp $(docker create --name tc hashicorp/terraform:latest):/bin/terraform ./terraform

ls terraform

![image](https://github.com/user-attachments/assets/ef2a0453-14b4-4027-8d4b-8906e92f6b28)


---

