FROM ubuntu:16.04
LABEL maintainer="ghilbut@gmail.com"

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

COPY uwsgi.ini  /ghilbut/uwsgi.ini
COPY myproject  /ghilbut/myproject
WORKDIR /ghilbut/myproject
RUN python3 manage.py collectstatic

VOLUME ["/ghilbut/static", "/ghilbut/uwsgi"]

ENTRYPOINT [ "/usr/bin/uwsgi", "--ini", "/ghilbut/uwsgi.ini" ]
