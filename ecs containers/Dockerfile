FROM redhat/redhat/ubi8

LABEL maintainer="Alin"

RUN yum -y install httpd

COPY index.html /var/www/html/

COPY containerandcat*.jpg /var/www/html/

ENTRYPOINT ["/user/bin/httpd","-D","FOREGROUND"]

EXPOSE 80