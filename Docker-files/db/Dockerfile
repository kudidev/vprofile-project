FROM mysql:latest
LABEL "project"="vprofile"
LABEL "Author"="kudidev"

################  NEED TO SET PASSWORD FOR DB #################
ENV MYSQL_ROOT_PASSWORD="vprodbpass"
ENV MYSQL_DATABASE="accounts"

ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql
