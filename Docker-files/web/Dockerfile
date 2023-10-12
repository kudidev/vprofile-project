FROM nginx:latest
LABEL "project"="vprofile"
LABEL "Author"="kudidev"

RUN rm -rf /etc/nginx/conf.d/default.conf
COPY nginvproapp.conf /etc/nginx/conf.d/vproapp.conf
