FROM ubuntu:14.04
RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*
RUN echo "<h1>Apache with Docker</h1>"> /var/www/html/index.html
ENV APACHE_RUN_USER=www-data
ENV APACHE_RUN_GROUP=www-data
ENV APACHE_LOG_DIR=/var/log/apache2
EXPOSE 80
ENTRYPOINT ["apache2ctl", "-D", "FOREGROUND"]
