FROM nginx:1.13.7
LABEL maintainer="ghilbut@gmail.com"

EXPOSE 8000

COPY uwsgi_params    /ghilbut/uwsgi_params
COPY myproject.conf  /etc/nginx/conf.d/myproject.conf
