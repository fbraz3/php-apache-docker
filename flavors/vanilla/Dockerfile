ARG PHP_VERSION=8.2
ARG BUILD_ARCH="amd64"
ARG BUILD_FLAVOR=""
FROM fbraz3/php-fpm:$PHP_VERSION$BUILD_FLAVOR
USER root

ARG PHP_VERSION
ARG BUILD_ARCH
ENV DEBIAN_FRONTEND=noninteractive
ENV ENTRYPOINT_COMMAND="/usr/bin/tail -f /var/log/apache2/access.log"

# TODO list
# Assess httpd estructure                      | OK
# Template vhost (prepare to pma)              | OK
# Template monit config                        | OK
# copy entrypoint to /entrypoints folder       | OK
# compile and test loccaly                     | OK
# remove unused files                          |
# Create gh actions for build and push         |
# Define tests for each flavor                 |
# Implement tests for each flavor              |
# refactor README.md                           |
# add both httpd and apache2 variants          |
# enable cron build                            |
# Update readme with deepwiki link             |
# Create build status badges                   |
# merge to main branch                         |
# triger manual builds                         |
# Create deepwiki page                         |
# Enable funding on github                     |
# Update dockerhub images description          |


COPY ../../assets/scripts/docker-entrypoint.sh /entrypoints/2-apache2-entrypoint.sh
RUN chmod +x /entrypoints/2-apache2-entrypoint.sh

#ensure php repo is up-to-date
RUN for i in {1..10}; do \
      add-apt-repository -y ppa:ondrej/php && break || sleep 10; \
    done

RUN /usr/bin/unattended-upgrades -v;

RUN for i in {1..10}; do \
      apt-get install -fy apache2 && break || sleep 10; \
    done

COPY ../../assets/monit/apache2 /etc/monit/conf-enabled/
COPY ../../assets/apache2/000-default.conf /etc/apache2/sites-enabled/000-default.conf

RUN a2enmod rewrite; \
    a2enmod proxy; \
    a2enmod proxy_fcgi

RUN mkdir /etc/apache2/custom-conf/; \
    touch /etc/apache2/custom-conf/dummy.conf;

# Fix Permissions
RUN chown -R www-data. /var/log/apache2; \
    chown -R www-data. /var/lib/apache2; \
    chown -R www-data. /etc/apache2/custom-conf/; \
    chown -R www-data. /etc/apache2/sites-enabled/; \
    chown -R www-data. /etc/apache2/sites-available/; \
    rm -rf /var/run/apache2/apache2.pid; \
    chown www-data. /var/run/apache2/;

WORKDIR /app
USER www-data
ENTRYPOINT ["/docker-entrypoint.sh"]
