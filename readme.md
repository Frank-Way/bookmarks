# Демо-соцсеть для обмена изображениями с различных интернет-источников

### Краткое описание

Проект создан в рамках изучения возможностей фреймворка Django версии 2. 

Проект поддерживает следующий набор функций:

- регистрация (в т.ч. с помощью Facebook, Google и Twitter);
- добавление изображений с других сайтов с помощью букмарклета;
- подписка на других пользователей, оценка изображений (т.н. лайки).

### Ключевые особенности

- использование СУДБ **PostgreSQL**;

### Подготовка к запуску

Для запуска проекта необходимо выполнить следующие шаги:

1. Установить Python 3.6.5

        sudo apt-get update &&
        sudo apt-get install python3.6.5 &&

2. Настроить PostgreSQL

    2.1 Установить PostgreSQL

        sudo apt install postgresql postgresql-contrib

    2.2 Создать роль
        
        sudo -u postgres createsuperuser --interactive

    2.3 Создать базу данных

        sudo -u <созданная_роль> createdb <имя_базы>

3. Настроить Ngrok

    3.1 Установить Ngrok

        curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null &&
              echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list &&
              sudo apt update && sudo apt install ngrok

    3.2 Зарегистрировать учётную запись (https://dashboard.ngrok.com/signup)

    3.3 Подключить учётную запись

        ngrok authtoken <токен_полученный_после_регистрации>

4. Создать и активировать виртуальную среду (опционально)

        mkdir project_directory &&
        cd project_directory &&
        python -m venv env &&
        source env/bin/activate  # на Windows 'env\Scripts\activate' &&

5. Установить зависимости проекта

        pip install -r requirements.txt

6. Загрузить проект

        git clone https://github.com/Frank-Way/neural-network.git

7. Настроить подключение к БД
    
    7.а Установить переменные окружения:

    - POSTGRES_DBNAME - имя созданной БД;
    - POSTGRES_USERNAME - имя созданной роли;
    - POSTGRES_PASSWORD - пароль созданной роли;
    - POSTGRES_HOST - хост, на котором запущена СУБД;
    - POSTGRES_PORT - порт, на котором запущена СУБД
   
    7.б Записать значения прямо в код в файле **bookmarks/settings.py**

       DATABASES = {
           'default': {
               'ENGINE': 'django.db.backends.postgresql',
               'NAME': '<имя созданной БД>',
               'USER': '<имя созданной роли>',
               'PASSWORD': '<пароль созданной роли>',
               'HOST': '<хост, на котором запущена СУБД>',
               'PORT': '<порт, на котором запущена СУБД>',
           }
       }

8. Настроить подключение к сервисам для внешней аутентификации (опционально)

    8.а Устновить переменные окружения:

    - FACEBOOK_KEY - ключ Facebook-приложения
    - FACEBOOK_SECRET - секретный токен Facebook
    - TWITTER_KEY - ключ Twitter-приложения
    - TWITTER_SECRET - секретный токен Twitter
    - GOOGLE_OAUTH2_KEY - ключ Google-приложения
    - GOOGLE_OAUTH2_SECRET - секретный Google Facebook

    8.б Записать значения в код в файле **bookmarks/settings.py**

        SOCIAL_AUTH_FACEBOOK_KEY = '<ключ Facebook-приложения>'  # Facebook App ID
        SOCIAL_AUTH_FACEBOOK_SECRET = '<секретный токен Facebook>'  # Facebook App Secret
        
        SOCIAL_AUTH_TWITTER_KEY = '<ключ Twitter-приложения>'  # Twitter Consumer Key
        SOCIAL_AUTH_TWITTER_SECRET = '<секретный токен Twitter>'  # Twitter Consumer Secret
        
        SOCIAL_AUTH_GOOGLE_OAUTH2_KEY = '<ключ Google-приложения>'  # Google Consumer Key
        SOCIAL_AUTH_GOOGLE_OAUTH2_SECRET = '<секретный Google Facebook>'  # Google Consumer Secret

9. Сгенерировать схему БД

        python manage.py makemigrations &&
        python manage.py migrate

10. Создать учётную запись администратора

        python manage.py createsuperuser

### Запуск

Для запуска проекта необходимо выполнить следующие шаги:

1. Запустить утилиту Ngrok для создания туннеля к хосту localhost:8000

        ngrok http 8000

    После запуска утилиты вышеуказанный хост будет доступен "извне" по некоторому 
    сгенерированному адресу формата "<...>.ngrok.io", а по адресу 
    http://127.0.0.1:4040 будет доступен интерфейс для обзора проходящего через 
    созданный туннель трафика.

2. Вписать полученный адрес в файле **images/templates/bookmarklet_launcher.js**
       
        ).src='https://<...>.ngrok.io/static/js/bookmarklet.js?r=' +

   и в файле **images/static/js/bookmarklet.js**

        var site_url = 'https://<...>.ngrok.io/';

3. Запустить сервер приложения

        python manage.py runserver

### Краткое руководство

После запуска приложения по адресу "<host>/admin/" доступна панель администратора.

Для использования приложения в 'обычном' режиме необходимо перейти по адресу
"<host>/account/", зарегистрироваться и залогиниться. После чего на странице 
"Dashboard" необходимо найти кнопку букмарклета с текстом "BOOKMARK IT" и 
добавить её в закладки.

Для загрузки в приложение подходят изображения, записанные на веб-странице в 
тег "img" и имеющие полный адрес (url сайта + путь к изображению + расширение).
Пример сайта, изображения с которого могут быть загружены с помощью 
разработанного букмарклета - https://www.shutterstock.com/ru/photos. 

Для загрузки изображения необходимо найти веб-страницу с изображениями, 
запустить букмарклет из закладок, в появившейся панели в левом верхнем углу 
страницы щёлкнуть ЛКМ по необходимому изображению, в новом окне добавить 
название и описание изображения, подтвердить введённую информацию.

Загруженное изображение могут оценивать другие пользователи. Пользователи могут 
подписываться друг на друга.