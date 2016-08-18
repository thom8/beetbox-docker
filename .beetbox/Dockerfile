FROM beet/box

ENV BEET_PROFILE 'docker'

# install packages.
RUN apt-get -qq update && apt-get install -y \
    supervisor \
    openssh-server

# And we want the Vagrant SSH key to be added
RUN mkdir /root/.ssh && chmod 700 /root/.ssh
ADD https://github.com/mitchellh/vagrant/raw/master/keys/vagrant.pub \
    /root/.ssh/authorized_keys
RUN chmod 400 /root/.ssh/authorized_keys \
    && chown root:root /root/.ssh/* \
    && chmod 600 /root/.ssh/*

# Create required directories
RUN mkdir -p /var/log/supervisor
RUN mkdir -p /var/run/sshd

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
	} | tee /etc/supervisor/conf.d/supervisord.conf

# Fix stdin not being a tty.
RUN sed -i '/tty/!s/mesg n/tty -s \&\& mesg n/' /root/.profile

VOLUME ["/var/beetbox"]

CMD ["supervisord", "-n"]
