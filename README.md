# docker-django-uwsgi

## A. environments

- python 3.x
- django 1.11.x

## B. create project environments

```bash
$ mkdir nginx django
$ cd django
$ django-admin startproject myproject
$ cd myproject
$ python3 manage.py runserver 0.0.0.0:8000
```

## C. set docker+uwsgi environments

### 1. create files

#### 1-1. django/uwsgi.ini

```ini
[uwsgi]
chdir        = /ghilbut/myproject
plugins      = python3
module       = myproject.wsgi:application
env          = DJANGO_SETTINGS_MODULE=myproject.settings

master       = true
processes    = 1
threads      = 4
http-socket  = 0.0.0.0:8000

chmod-socket = 666
vaccum       = true
pidfile      = /ghilbut/uwsgi.pid

enable-threads = true
single-interpreter = true
lazy-apps = true
```

#### 1-2. django/uwsgi_param

```
uwsgi_param  QUERY_STRING       $query_string;
uwsgi_param  REQUEST_METHOD     $request_method;
uwsgi_param  CONTENT_TYPE       $content_type;
uwsgi_param  CONTENT_LENGTH     $content_length;

uwsgi_param  REQUEST_URI        $request_uri;
uwsgi_param  PATH_INFO          $document_uri;
uwsgi_param  DOCUMENT_ROOT      $document_root;
uwsgi_param  SERVER_PROTOCOL    $server_protocol;
uwsgi_param  HTTPS              $https if_not_empty;

uwsgi_param  REMOTE_ADDR        $remote_addr;
uwsgi_param  REMOTE_PORT        $remote_port;
uwsgi_param  SERVER_PORT        $server_port;
uwsgi_param  SERVER_NAME        $server_name;
```

#### 1-3. django/Dockerfile

```
FROM ubuntu:16.04
LABEL maintainer="ghilbut@gmail.com"

EXPOSE 8000

RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y \
            python3 \
            python3-pip \
            libmysqlclient-dev \
            uwsgi \
            uwsgi-plugin-python3

COPY requirements.txt .
RUN pip3 install --upgrade pip \
 && pip3 install --no-cache-dir -r requirements.txt

COPY uwsgi.ini    /ghilbut/uwsgi.ini
COPY uwsgi_param  /ghilbut/uwsgi_param
COPY myproject    /ghilbut/myproject

ENTRYPOINT [ "/usr/bin/uwsgi", "--ini", "/ghilbut/uwsgi.ini" ]
```

#### 1-4. django/.dockerignore

```
**/.DS_Store
**/__pycache__
**/db.sqlite3
**/*.pyc
```

#### 1-5. django/requirements.txt

```
Django==1.11.7
mysqlclient==1.3.12
```

### 2. build and run docker image

```
$ docker build -t myproject:0.1 .
$ docker run -it --rm --name myproject -p 8000:8000 myproject:0.1
```

## D. create docker+nginx environments


