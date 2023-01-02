FROM python:3.6-alpine
MAINTAINER THIONGANE ABDOULAYE
#Ajout utilisateur qui va lancer le conteneur (abdou)
RUN addgroup -S abdou -g 433 && \
adduser -u 431 -S -G abdou -s /sbin/nologin abdou
#Definition du repertoire de travail
WORKDIR /opt
#Ajout donnees web sur le workingdir
ADD . /opt
#installation de flask
RUN pip install flask==1.1.2
#Port d'ecoute
EXPOSE 8080
#Variables d'environnement
ENV ODOO_URL https://www.odoo.com/
ENV PGADMIN_URL https://www.pgadmin.org/
#Run application
ENTRYPOINT [ "python", "./app.py" ]
USER abdou