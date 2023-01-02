FROM python:3.6-alpine
MAINTAINER THIONGANE ABDOULAYE
RUN addgroup -S abdou -g 433 && \
adduser -u 431 -S -G abdou -s /sbin/nologin abdou
WORKDIR /opt
ADD . /opt
RUN pip install flask==1.1.2
EXPOSE 8080
ENV ODOO_URL https://www.odoo.com/
ENV PGADMIN_URL https://www.pgadmin.org/
ENTRYPOINT [ "python", "./app.py" ]
USER abdou
