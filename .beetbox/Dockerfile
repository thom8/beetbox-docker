FROM beet/box

ENV BEET_PROFILE 'docker'

# install packages.
RUN apt-get -qq update && apt-get install -y \
    supervisor \
    openssh-server

# Add startup script.
RUN set -ex \
	&& { \
		echo '#!/bin/bash -e'; \
		echo; \
		echo '[ ! -f "/installed" ] && /beetbox/provisioning/beetbox.sh'; \
		echo 'touch /installed'; \
		echo '/usr/sbin/apache2ctl stop > /dev/null 2>&1'; \
		echo 'service php5.6-fpm stop > /dev/null 2>&1'; \
		echo 'service mailhog stop > /dev/null 2>&1'; \
		echo 'supervisord -n -c /etc/supervisor/conf.d/supervisord.conf'; \
	} | tee /start.sh

# Create required directories
RUN mkdir -p /var/log/supervisor
RUN mkdir -p /var/run/sshd
RUN mkdir -p /run/php/

# Create supervisord conf.
RUN set -ex \
	&& { \
        echo '[supervisord]'; \
        echo 'nodaemon = true'; \
        echo 'user = root'; \
        echo; \
        echo '[program:sshd]'; \
        echo 'command = /usr/sbin/sshd -D'; \
        echo 'autostart = true'; \
        echo; \
        echo '[program:apache]'; \
        echo 'command = /usr/sbin/apache2ctl -D FOREGROUND'; \
        echo 'autostart = true'; \
        echo; \
        echo '[program:php5-fpm]'; \
        echo 'command = /usr/sbin/php-fpm5.6 -F'; \
        echo 'autostart = true'; \
        echo; \
        echo '[program:mailhog]'; \
        echo 'command = /opt/mailhog/mailhog'; \
        echo 'autostart = true'; \
	} | tee /etc/supervisor/conf.d/supervisord.conf

VOLUME ["/var/beetbox"]

CMD ["/bin/bash", "/start.sh"]
