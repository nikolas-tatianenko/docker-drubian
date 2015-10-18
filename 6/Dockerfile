FROM debian:6

MAINTAINER Ain Tohvri <ain@flashbit.net>

ENV USERNAME deploy
ENV HOME /home/$USERNAME

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
  libapache2-mod-php5
  #supervisor

# Necessary to fall through Drupal installation.
ADD config/dbconfig-common/drupal6.conf /etc/dbconfig-common/drupal6.conf

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y drupal6 drush

#RUN locale-gen en_GB.UTF-8 \
  #&& update-locale LANG=en_GB.UTF-8

RUN adduser --home $HOME --disabled-password --gecos '' $USERNAME
RUN mkdir -p $HOME/.ssh
RUN touch $HOME/.ssh/known_hosts
RUN echo "%$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN echo 'ServerName drupal.dev' >> /etc/apache2/apache2.conf
RUN echo 'sudo service apache2 start' >> /etc/bash.bashrc \
  && /etc/init.d/apache2 start

COPY config/apache/sites-available/drupal.dev /etc/apache2/sites-available/
RUN ls -la /etc/apache2/sites-available
RUN a2enmod rewrite \
  && a2dissite 000-default \
  && a2ensite drupal.dev \
  && /etc/init.d/apache2 stop

# Supervisor handles startup of multiple services.
#COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 80

USER $USERNAME
WORKDIR /docker

#CMD ["sudo", "/usr/bin/supervisord"]
CMD ["sudo", "/etc/init.d/apache2", "start"]
