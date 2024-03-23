https://editor.swagger.io/ -- для чтения openapi.json

_______

 
 # ШБР Лето 2023. Вступительное задание

Вы устроились в Яндекс, и вам нужно запустить сервис Яндекс Лавка.
Ваша первая задача — разработать новый REST API-сервис, который будет регистрировать курьеров, добавлять новые заказы и 
распределять их по курьерам.


## Задание 1. REST API

В качестве базовой функциональности сервиса необходимо реализовать 7 базовых методов.

Для всех методов в случае корректного ответа ожидается ответ `HTTP 200 OK`.

### POST /couriers
Для загрузки списка курьеров в систему запланирован описанный ниже интерфейс.

Обработчик принимает на вход список в формате json с данными о курьерах и графиком их работы.

Курьеры работают только в заранее определенных районах, а также различаются по типу: пеший, велокурьер и 
курьер на автомобиле. От типа зависит объем заказов, которые перевозит курьер.
Районы задаются целыми положительными числами. График работы задается списком строк формата `HH:MM-HH:MM`.

### GET /couriers/{courier_id}

Возвращает информацию о курьере.

### GET /couriers

Возвращает информацию о всех курьерах.

У метода есть параметры `offset` и `limit`, чтобы обеспечить постраничную выдачу.
Если:
* `offset` или `limit` не передаются, по умолчанию нужно считать, что `offset = 0`, `limit = 1`;
* офферов по заданным `offset` и `limit` не найдено, нужно возвращать пустой список `couriers`.

### POST /orders

Принимает на вход список с данными о заказах в формате json. У заказа отображаются характеристики — вес, район, 
время доставки и цена.
Время доставки - строка в формате HH:MM-HH:MM, где HH - часы (от 0 до 23) и MM - минуты (от 0 до 59). Примеры: “09:00-11:00”, “12:00-23:00”, “00:00-23:59”.


### GET /orders/{order_id}

Возвращает информацию о заказе по его идентификатору, а также дополнительную информацию: вес заказа, район доставки, 
промежутки времени, в которые удобно принять заказ.

### GET /orders

Возвращает информацию о всех заказах, а также их дополнительную информацию: вес заказа, район доставки, промежутки времени, в которые удобно принять заказ.

У метода есть параметры `offset` и `limit`, чтобы обеспечить постраничную выдачу.
Если:
* `offset` или `limit` не передаются, по умолчанию нужно считать, что `offset = 0`, `limit = 1`;
* офферов по заданным `offset` и `limit` не найдено, нужно возвращать пустой список `orders`.

### POST /orders/complete

Принимает массив объектов, состоящий из трех полей: id курьера, id заказа и время выполнения заказа, после отмечает, что заказ выполнен.

Если заказ:
* не найден, был назначен на другого курьера или не назначен совсем — следует вернуть ошибку `HTTP 400 Bad Request`.
* выполнен успешно — следует выводить `HTTP 200 OK` и идентификатор завершенного заказа.

Обработчик должен быть идемпотентным.


## Задание 2. Rate limiter

Каждый большой сервис с API, открытым из интернета, должен ограничивать количество входящих запросов.
Для этого используется rate limiter. Вам нужно реализовать такое решение для разрабатываемого сервиса.

Для решения задачи можно написать собственную реализацию или использовать известное готовое решение.
Сервис должен ограничивать нагрузку в 10 RPS на каждый эндпоинт. Если допустимое количество запросов превышено, сервис должен отвечать кодом 429.


## Приложение. Как выполнить задание?

1. Сгенерировать ssh-ключ
2. Перейти в [профиль](https://school.yandex.ru/profile/)
3. Нажать кнопку "Редактировать профиль"
4. Ввести Публичный ключ и нажать кнопку "Сохранить"
5. Вернуться в задачу в LMS, в комментариях появятся ссылки на 2 репозитория:
    * с тестовым заданием
    * с шаблоном решения

6. Выполнить `git clone` репозитория с текстом задания, в нем можно найти более подробные инструкции по выполнению
7. Выполнить `git clone` репозитория с шаблоном решения.
В данном репозитории нужно выполнить задание, сделать `git commit` и отправить результат на сервер `git push`.
После того, как выполнен git push, нужно вернуться в LMS на страницу с заданием и нажать кнопку **Отправить ответ**.
Обязательно напишите любой текст в поле ответа (например, "ОК"), иначе кнопка отправки будет недоступна.

## Приложение. Требования к решению.

1. Решение должно быть представлено в виде Dockerfile в котором происходит сборка, настройка и запуск решения;
2. Сервис должен обрабатывать входящие запросы на порту 8080;
3. На порту 5432 будет доступна БД PostgreSQL 15.2. Авторизация происходит по логину и паролю: user=postgres password=password;
4. В Dockerfile в репозитории с решением базовый образ строго зафиксирован. Изменять образ в директиве FROM нельзя. При этом можно добавлять в контейнер необходимые пакеты если это требуется.
