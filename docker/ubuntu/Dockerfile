FROM ubuntu:21.04

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
    && apt-get install -y curl zip unzip python2 cron vim software-properties-common \
    && add-apt-repository ppa:obsproject/obs-studio \
    && apt-get update

# install obs studio.
RUN apt-get install -y ffmpeg v4l2loopback-dkms obs-studio vlc

# cleaning file.
RUN apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# cron setting.
COPY ./docker/ubuntu/crontab /etc/cron.d/crontab
RUN chmod 0644 /etc/cron.d/crontab \
    && crontab /etc/cron.d/crontab \
    && ln -sf /proc/1/fd/1 /var/log/cron.log \
    && service cron stop

# code setting.
COPY ./src /var/www/html
