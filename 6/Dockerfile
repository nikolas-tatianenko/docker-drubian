FROM debian:6

MAINTAINER Ain Tohvri <ain@flashbit.net>

ENV USERNAME deploy

# Apache environment variables. Override in Compose if required.
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_SERVERADMIN admin@localhost
ENV APACHE_SERVERNAME drupal.dev
ENV APACHE_SERVERALIAS www.docker.dev
ENV APACHE_DOCUMENTROOT /var/www

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
  debconf-utils \
  apt-utils \
  curl \
  git \
  wget \
  imagemagick \
  vim \
  apache2 \
  php5 \
  libapache2-mod-php5 \
  sudo

# Necessary to fall through Drupal installation.
ADD config/dbconfig-common/drupal6.conf /etc/dbconfig-common/drupal6.conf

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y drupal6 drush

RUN adduser -m --disabled-password --gecos '' -G sudo -s bash $USERNAME
RUN mkdir -p $HOME/.ssh
RUN touch $HOME/.ssh/known_hosts
#RUN usermod -a -G sudo $USERNAME

RUN echo 'sudo service apache2 start' >> /etc/bash.bashrc \
  && /etc/init.d/apache2 start
COPY config/apache/sites-available/drupal.dev /etc/apache2/sites-available/
COPY config/apache/apache2-foreground /usr/local/bin/
RUN a2enmod rewrite \
  && a2dissite 000-default \
  && a2ensite drupal.dev \
  && /etc/init.d/apache2 stop

EXPOSE 80

USER $USERNAME
WORKDIR /home/$USERNAME

CMD ["apache2-foreground"]
